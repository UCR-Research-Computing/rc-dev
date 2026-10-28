---
title: "Using CephRDS from other languages and tools (AWS CLI, R, s5cmd, Julia, MATLAB) and from HPCC"
kb_id: KB033
topic: Storage
description: "The same three settings work in every S3 tool: endpoint https://rds.ucr.edu, path-style addressing, any region. Examples for the AWS CLI, s5cmd, R, Julia, MATLAB and Go, plus moving data in and out of HPCC jobs."
audience: "UCR Faculty, Postdocs, Researchers & Students with a CephRDS bucket"
reviewed: 2026-10-28
owner: Research Computing
review_notes:
  - "2026-10-28: new article. Settings taken from KB013/KB018 and from the rds-cli 0.1.13 client (endpoint https://rds.ucr.edu, path-style addressing, no region). Not yet run against rds.ucr.edu."
  - "CHECK: run each example against a real bucket over the VPN, and the HPCC section from an HPCC login node and a batch job."
  - "CHECK: newer AWS CLI and SDK releases add request checksums that some Ceph gateways reject; confirm whether the when_required settings are needed for rds.ucr.edu."
  - "CHECK: R aws.s3 with region empty and use_https; MATLAB AWS_S3_ENDPOINT without https://; Go UsePathStyle."
---

CephRDS speaks the Amazon S3 protocol, so almost any tool or language that can talk to Amazon S3 can talk to CephRDS. You only change where it connects. This page shows the settings once, then short examples for common tools and languages, and how to use CephRDS from the HPCC cluster.

For Python, see [KB018](../kb018-cephrds-python-boto3/). For the simplest command-line tool, see [KB032: rds-cli](../kb032-cephrds-rds-cli/). To request storage and keys, see [KB013](../kb013-cephrds-onboarding/).

## The three settings every tool needs

| Setting | Value for CephRDS | Why |
| --- | --- | --- |
| Endpoint | `https://rds.ucr.edu` | Points the tool at campus storage instead of Amazon |
| Addressing | Path-style | Bucket names go in the path (`rds.ucr.edu/my-lab-bucket/...`), not in the hostname |
| Region | `us-east-1` or blank | CephRDS has no regions; some tools insist on a value, and any value works |

Plus your Access Key ID and Secret Access Key. CephRDS is reachable from the campus network only: connect to the UCR VPN when off campus (HPCC is on campus already).

**Keep keys out of your code.** Every example below reads them from environment variables. Set them in your shell, or in a file only you can read:

```bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
```

On Windows PowerShell use `$env:AWS_ACCESS_KEY_ID="..."` instead.

## AWS CLI

The official Amazon command-line tool. Install it from the [AWS CLI install guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

Create a profile for CephRDS so it doesn't mix with any real AWS account. Add this to `~/.aws/config`:

```ini
[profile cephrds]
region = us-east-1
endpoint_url = https://rds.ucr.edu
s3 =
    addressing_style = path
request_checksum_calculation = when_required
response_checksum_validation = when_required
```

And your keys to `~/.aws/credentials`:

```ini
[cephrds]
aws_access_key_id = your-access-key
aws_secret_access_key = your-secret-key
```

Then:

```bash
aws --profile cephrds s3 ls
aws --profile cephrds s3 ls s3://my-lab-bucket/
aws --profile cephrds s3 cp data.csv s3://my-lab-bucket/study1/
aws --profile cephrds s3 sync ./results/ s3://my-lab-bucket/results/
aws --profile cephrds s3 cp s3://my-lab-bucket/study1/ ./study1/ --recursive
```

The two `checksum` lines turn off a newer AWS feature that non-Amazon storage does not always support. Leave them in.

## s5cmd (fast bulk transfers)

[s5cmd](https://github.com/peak/s5cmd) is a single small program built for moving many files quickly. It is a good choice for very large folders. It reads the same environment variables:

```bash
s5cmd --endpoint-url https://rds.ucr.edu ls s3://my-lab-bucket/
s5cmd --endpoint-url https://rds.ucr.edu cp 'images/*' s3://my-lab-bucket/images/
s5cmd --endpoint-url https://rds.ucr.edu cp 's3://my-lab-bucket/images/*' ./images/
```

Quote any path containing `*` so your shell doesn't expand it first.

## rclone

rclone covers copying, syncing and mounting on Linux, macOS and Windows. Setup is in [KB013](../kb013-cephrds-onboarding/) and mounting in [KB020](../kb020-cephrds-mounting-folders/). For scripts, you can skip the interactive setup by using environment variables:

```bash
export RCLONE_CONFIG_CEPHRDS_TYPE=s3
export RCLONE_CONFIG_CEPHRDS_PROVIDER=Ceph
export RCLONE_CONFIG_CEPHRDS_ENDPOINT=https://rds.ucr.edu
export RCLONE_CONFIG_CEPHRDS_ENV_AUTH=true
rclone lsd cephrds:
rclone copy ./results cephrds:my-lab-bucket/results -P
```

## R

The [aws.s3](https://cran.r-project.org/package=aws.s3) package works with CephRDS. Give it the host without `https://`, an empty region and path-style requests:

```r
install.packages("aws.s3")
library(aws.s3)

Sys.setenv(AWS_S3_ENDPOINT = "rds.ucr.edu")   # keys come from AWS_ACCESS_KEY_ID / AWS_SECRET_ACCESS_KEY

bucket <- "my-lab-bucket"

# List files
files <- get_bucket_df(bucket, region = "", use_https = TRUE, url_style = "path")
head(files$Key)

# Upload and download a file
put_object(file = "results.csv", object = "study1/results.csv",
           bucket = bucket, region = "", url_style = "path")
save_object(object = "study1/results.csv", file = "results_copy.csv",
            bucket = bucket, region = "", url_style = "path")

# Read a CSV straight into a data frame
df <- s3read_using(read.csv, object = "study1/results.csv",
                   bucket = bucket, opts = list(region = "", url_style = "path"))
```

If your lab already uses the [paws](https://paws-r-sdk.github.io/) package, set `endpoint = "https://rds.ucr.edu"` and `s3_force_path_style = TRUE` in its `config` list.

## Julia

With [AWS.jl](https://github.com/JuliaCloud/AWS.jl) and [AWSS3.jl](https://github.com/JuliaCloud/AWSS3.jl):

```julia
using AWS, AWSS3

struct CephRDS <: AbstractAWSConfig
    creds::AWSCredentials
end
AWS.region(::CephRDS) = "us-east-1"
AWS.credentials(c::CephRDS) = c.creds
AWS.generate_service_url(::CephRDS, service::String, resource::String) =
    "https://rds.ucr.edu$resource"

cfg = CephRDS(AWSCredentials(ENV["AWS_ACCESS_KEY_ID"], ENV["AWS_SECRET_ACCESS_KEY"]))

s3_list_keys(cfg, "my-lab-bucket") |> collect
s3_put(cfg, "my-lab-bucket", "study1/hello.txt", "hello from Julia")
String(s3_get(cfg, "my-lab-bucket", "study1/hello.txt"))
```

The custom config sends every request to CephRDS with the bucket in the path.

## MATLAB

MATLAB reads `s3://` paths directly once three environment variables are set. Set them before the first S3 access in a session:

```matlab
setenv("AWS_ACCESS_KEY_ID", "your-access-key");      % or set these outside MATLAB
setenv("AWS_SECRET_ACCESS_KEY", "your-secret-key");
setenv("AWS_S3_ENDPOINT", "rds.ucr.edu");             % host only, no https://
setenv("AWS_DEFAULT_REGION", "us-east-1");

T = readtable("s3://my-lab-bucket/study1/results.csv");
ds = imageDatastore("s3://my-lab-bucket/images/", "IncludeSubfolders", true);
writetable(T, "s3://my-lab-bucket/study1/results_copy.csv");
```

This works with `readtable`, `imread`, `datastore` and most other file functions.

## Go

With the AWS SDK for Go v2:

```go
cfg, err := config.LoadDefaultConfig(ctx, config.WithRegion("us-east-1"))
if err != nil { log.Fatal(err) }

client := s3.NewFromConfig(cfg, func(o *s3.Options) {
    o.BaseEndpoint = aws.String("https://rds.ucr.edu")
    o.UsePathStyle = true
})

out, err := client.ListObjectsV2(ctx, &s3.ListObjectsV2Input{Bucket: aws.String("my-lab-bucket")})
```

Other SDKs (Java, JavaScript, C++, Rust) follow the same pattern: set the endpoint override and turn on path-style or "force path style".

## Using CephRDS from the HPCC cluster

The [HPCC cluster](../../services/hpcc/) is on the campus network, so its login and compute nodes reach CephRDS directly, with no VPN. A common pattern is to keep the lab's long-term copy in CephRDS, copy what a job needs to fast cluster storage, run the job, and copy the results back.

**One-time setup on a login node**

1. Install a tool in your home directory. rclone and rds-cli both install without admin rights:

   ```bash
   uv tool install git+https://github.com/UCR-Research-Computing/rds-cli.git
   # or download the rclone Linux binary from rclone.org into ~/bin
   ```

   Check first whether the HPCC already provides rclone as a module (`module avail rclone`).

2. Store your keys in a file only you can read:

   ```bash
   mkdir -p ~/.cephrds && chmod 700 ~/.cephrds
   cat > ~/.cephrds/env <<'EOF'
   export AWS_ACCESS_KEY_ID="your-access-key"
   export AWS_SECRET_ACCESS_KEY="your-secret-key"
   EOF
   chmod 600 ~/.cephrds/env
   ```

**In a batch job**

```bash
#!/bin/bash
#SBATCH --job-name=cephrds-example
#SBATCH --time=02:00:00
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G

source ~/.cephrds/env
WORK=/path/to/your/scratch/$SLURM_JOB_ID     # use your HPCC scratch or bigdata path
mkdir -p "$WORK/input" "$WORK/output"

# 1. Bring the input down from CephRDS
rds-cli download study1/input/ -b my-lab-bucket -r -d "$WORK/input/"

# 2. Run the analysis on local cluster storage
my_analysis --in "$WORK/input" --out "$WORK/output"

# 3. Send the results back to CephRDS
rds-cli upload "$WORK/output/" -b my-lab-bucket -k study1/results/$SLURM_JOB_ID/
```

The same steps work with `rclone copy` or the AWS CLI. Tips:

- **Move data in bulk.** Copy what the job needs at the start rather than reading many small files from CephRDS during the run. Computation runs faster on cluster storage.
- **Large transfers belong in a job**, not on a login node, unless they are short.
- **Keep the keys file private** (`chmod 600`). Anyone who can read it can use your bucket.

## Troubleshooting

- **Connection times out**: you are off campus. Connect to the UCR VPN (not needed on HPCC).
- **SignatureDoesNotMatch or "Invalid region"**: set a region (`us-east-1`) and path-style addressing.
- **The tool tries to reach `my-lab-bucket.rds.ucr.edu`**: path-style addressing is off. Turn it on (the setting names are shown above for each tool).
- **Errors mentioning checksums (XAmzContentSHA256Mismatch, MissingContentLength)**: on the AWS CLI and newer SDKs, set request and response checksums to `when_required`, as in the AWS CLI profile above.

## Getting help

Email [research-computing@ucr.edu](mailto:research-computing@ucr.edu) with the tool, the command and the full error message (never include your secret key).
