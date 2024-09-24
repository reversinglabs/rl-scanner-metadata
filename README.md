# ReversingLabs Spectra Assure: Public metadata

**This repository is intended for ReversingLabs partners who want to integrate with the Spectra Assure platform in their solutions and workflows.**

The repository contains metadata source files for Spectra Assure policies.
Spectra Assure products use this same metadata in analysis reports.
Partners can use this metadata to transform or supplement the analysis reports; for example, to develop their own report visualizations, or to localize reports into various languages.

**Maintained by:**

- [ReversingLabs](https://www.reversinglabs.com/)

**Where to get help:**

- [Official Spectra Assure documentation](https://docs.secure.software/)
- [ReversingLabs Support](mailto:support@reversinglabs.com)


## What is Spectra Assure?

The Spectra Assure platform is a set of ReversingLabs solutions primarily designed for software assurance and software supply chain security use-cases. 

Spectra Assure helps users protect their software supply chains by analyzing compiled software packages, their components and third-party dependencies to detect exposures, reduce vulnerabilities, and eliminate threats before reaching production.

The platform offers flexible products suitable for different customer needs:

- `rl-secure`, a standalone CLI tool for Windows and Linux
- Spectra Assure Portal, a ReversingLabs-hosted SaaS solution with public APIs for advanced workflows
- Docker images and CI/CD integrations for deploying Spectra Assure in ephemeral environments


## What are Spectra Assure policies?

Policies are built-in rules that prescribe how software should behave in order to be considered secure. 

During analysis, Spectra Assure may detect that the analyzed software breaks those rules.
Such incidents are called "policy violations" or "issues", and displayed in the analysis reports to warn and inform the users.
Users can then take appropriate measures to resolve detected issues.

Every Spectra Assure policy:
- provides a detailed description of the detected issue and a set of recommended steps for resolving it
- defines the priority, severity, and effort required to resolve the detected issue
- sets the default CI/CD status (pass or fail) that the issue will trigger if it's detected in the software
- specifies the [ReversingLabs Level](https://docs.secure.software/concepts/levels) at which the issue will affect the CI/CD status.


## Overview

Spectra Assure policy metadata is provided as a set of JSON files in the `/data` directory.

Policies are grouped into JSON files according to types of issues they detect.

As a result, every JSON file corresponds to a specific policy category:

- `containers.json` - Container Security
- `hunting.json` - Threat Hunting and Differential Analysis
- `licenses.json` - License Compliance
- `linux.json` - Application Hardening (Linux)
- `secrets.json` - Sensitive Information
- `signatures.json` - Digital Signatures
- `threats.json` - Malware Detection
- `vulnerabilities.json` - Known Vulnerabilities 
- `windows.json` - Application Hardening (Windows)
- `integrity.json` - Package Integrity


### Policy metadata file structure

All JSON files have the same structure for policy metadata, regardless of the category.

Every policy is defined in its own top-level object and distinguished from other policies by a unique identifier (**policy ID**).
The first two letters of the ID indicate the policy type:

- `SQ*****` - software quality policies
- `TH*****` - threat hunting policies

The structure of the **policy ID** object is illustrated and described inline in the following example.
Objects and values can be empty, which is represented as `null`.
Empty arrays are represented as `[]`.

```
"SQ*****":              // Unique policy ID
{
  "quality": {          // Software quality properties associated with the policy.         
    "blocker": string   // CI/CD status set by the policy when the issue is detected. Can be one of: pass, fail
    "effort": string    // Estimated effort required to resolve the issue. Can be one of: high, medium, low
    "enabled": boolean  // Indicates if the policy is enabled in the default configuration.
    "priority": integer // Issue remediation priority indicated as a number from 0 (P0, highest) to 4 (P4, lowest).
    "rl-level": integer // ReversingLabs Level (0 to 5) at which the policy starts to set the CI/CD status to the
                           blocker value.
    "severity": string  // Estimated severity of the detected issue. Can be one of: high, medium, low
    "flags": null       // Internal; not relevant to end users. 
  },
  "assessment": {       // Risk assessment properties of the detected issue.
    "category": string  // ReversingLabs assessment category the detected issue is associated with.
                           Can be one of: containers, licenses, secrets, vulnerabilities, hardening, tampering, malware
    "status": string    // Status set for the assessment category when the issue is detected. 
                           Can be one of: pass, warning, fail
    "localization": [
      {
        "label": string     // Text displayed for the assessment category when the issue is detected.  
        "language": string  // Natural language used for the label value (ISO 639-1 standard code).
      }
    ]
  },
  "policy": {               // Human-readable details about the issue that the policy detects.
    "category": string      // Policy category. Corresponds to JSON filename except for hardening, which is split
                               into separate JSON files for Windows and Linux.
    "localization": [
      {
        "label": string         // Short summary of the detected issue.
        "description": string   // Detailed description of the detected issue.
        "language": string      // Natural language used for all text in this object (ISO 639-1 standard code).
        "steps": [              // Actions recommended by ReversingLabs for resolving the detected issue.
          {
            "content": string   // Short description of the recommended action. 
            "type": string      // Type of the recommended action. 
                                   Can be one of: consult, fix, investigate, practice, stop, update
          }
        ]
      }
    ]
  }
}
```


### Catalogue file

In addition to policy metadata JSON files, the `/data` directory contains the `catalogue.json` file.

The catalogue file provides an overview of all policy categories, maps policy categories to JSON files and ReversingLabs Assessment verticals, and defines policy ID ranges for every category.

The structure of objects in `ui-display.issues` is illustrated and described inline in the following example.
Objects and values can be empty, which is represented as `null`.
Empty arrays are represented as `[]`.

```
{
  "category": string               // Policy category name; used internally in metadata and other policy-related files.
    "policies": [
       string                      // Name(s) of JSON files containing metadata for policies in this category.
    ]
    "enumeration": {
       "prefix": string            // Two-letter prefix used for policy IDs in this category.
       "ranges": [
          {
             "id-label": null      // Policy ID label.
             "id-first": integer   // ID of the first policy in this category.
             "id-last": integer    // ID of the last policy in this category.
          }
       ]
    }
    "localization": [
          {
            "label": string       // Policy category label used in analysis reports and product UI.
            "language": string    // Language of the policy category label.
          }
    ]
}
```


The structure of objects in `ui-display.assessments` is illustrated and described inline in the following example.
Objects and values can be empty, which is represented as `null`.
Empty arrays are represented as `[]`.

```
{
  "category": string          // Policy category name.
  "vertical": string          // ReversingLabs Assessment vertical that the policy category belongs to.
  "localization": [
      {
        "label": string       // Policy category label used in ReversingLabs Assessment sections of the report and product UI.
        "language": string    // Language of the policy category label.
      }
  ]
}
```

## Versioning 

This repository always contains the latest version of metadata available in Spectra Assure products. 

The [VERSION](./VERSION) file indicates the specific Spectra Assure CLI version that the metadata is synchronized with.


## License

The contents of this repository are [MIT licensed](./LICENSE).
