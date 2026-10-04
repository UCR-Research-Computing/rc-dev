---
title: "UCR Data Security Plans (DSP)"
topic: Security
owner: Research Computing
redirect_from:
  - /Knowledge_Base/UCR_Data_Security_Plans.html
---

Conducting research with highly sensitive data - such as P3/P4 classified data, Protected Health Information (HIPAA), NIST 800-171 Rev 2 regulated data, Export Controlled data, or datasets governed by strict Data Use Agreements (DUAs) - requires formal planning and approval under University of California policy (such as IS-3) and any external regulations or agreements that apply.

This guide outlines the process for initiating a Data Security Plan (DSP), understanding your responsibilities, and securing the necessary infrastructure.

## 1. What is a Data Security Plan (DSP)?

At UCR, a Data Security Plan is a formal, comprehensive document that outlines the Roles, Responsibilities, Guidelines, Processes, and Technical Controls essential for safeguarding your research data. 

It serves as a blueprint, reviewed and approved by the Information Security Office (ISO), describing how your research environment is built and maintained securely. It covers critical areas such as:
*   **Data Flow:** How data enters, moves through, and exits your environment.
*   **Access Controls:** Who has access, how they authenticate (e.g., MFA), and the principle of least privilege.
*   **Encryption:** Standards for data at rest and data in transit.
*   **Incident Response:** Procedures for handling potential breaches or security events.

**[Download the official UCR Data Security Plan Template (Google Doc)](https://docs.google.com/document/d/17oO97C_AtGzAsno6se8MYcZlqfiv3BpvPurFnVosi_0/edit?usp=sharing)**

## 2. The Discovery Phase: Before We Build

When requesting infrastructure for a confidential dataset, the Research Computing team and the ISO must first establish the compliance baseline. Before any technical solutions are proposed or hardware is purchased, researchers must complete the following intake steps:

### A. PI Oversight is Mandatory
All formal Data Security Plans and secure infrastructure setups **must be anchored to a faculty data custodian**. If you are a student, postdoc, or staff member initiating the request, your Principal Investigator (PI) or Faculty Advisor must be explicitly included in the communication and approve the requests. They hold the ultimate responsibility for the data.

### B. Provide Governing Documents (DUA)
Technical controls are driven entirely by contractual and regulatory obligations. You must provide the **Data Use Agreement (DUA)**, the grant contract, or the specific security policy document provided by the dataset owner. We cannot design an appropriate environment without reviewing the exact stipulations regarding storage, access, encryption, and auditing required by the provider.

### C. Initial Project Overview
When contacting support, be prepared to provide a brief, high-level summary of:
*   What the data is (e.g., genomic sequences, student records, clinical data).
*   Who the provider is (e.g., NIH dbGaP, a corporate partner, Department of Defense).
*   The general scope and duration of the research.

## 3. Secure Compute Options

The specific constraints of your DUA and the data classification will dictate which approved infrastructure you must use. **UCR does not support or approve the use of standalone workstations in individual offices for highly sensitive data.** 

### Standard P3/P4 Research Data
For general sensitive research data classified as P3 or P4 (e.g., standard confidential data without federal defense or specialized enclave requirements):

*   **Option 1: Google Cloud Platform (Tier 2 Recharge)** 
    *   **Availability:** All UCR Researchers.
    *   **Description:** Standard secure project shells built within the Ursa Major GCP organization. These projects are isolated from other projects, without the additional logging and auditing of the enclave. 
    *   **Cost:** This is a **recharged service** requiring a grant-funded Chart of Accounts (COA) for Direct Recharge.

*   **Option 2: On-Premise Hosting (CHASS Server Room)**
    *   **Availability:** Strictly limited to researchers within the **College of Humanities, Arts, and Social Sciences (CHASS)**.
    *   **Description:** Physical server hosting within the secure CHASS datacenter environment. 

### High-Compliance Federal Data (CMMC, NIST 800-171, NIH dbGaP)
If your grant or DUA involves the Department of Defense (DoD), Department of Energy (DOE), NIH dbGaP, or mandates specific federal compliance frameworks like **CMMC** or **NIST 800-171**:

*   **The UCR Secure Enclave**
    *   **Availability:** All UCR Researchers with qualifying federal grants.
    *   **Description:** A highly specialized, purpose-built environment within Google Cloud designed to support the technical controls of NIST SP 800-171, including monitoring, restricted data transfer and audit logging. Suitability is decided in review. See [KB014](../kb014-secure-enclave-guide/).
    *   **Cost:** Due to the significant security overhead, this is a **recharged service** requiring a grant-funded Chart of Accounts (COA) for Direct Recharge.

## 4. Next Steps

If you need to begin the DSP process or request secure infrastructure, send a request to Research Computing (research-computing@ucr.edu) and include:
1. Copied your PI on the email.
2. Attached your DUA or data provider security requirements.
3. Provided a brief summary of your research goals.
