
# Requirements for a check (copied from checks/write.md)

If you'd like to add a check, make sure it meets the following criteria and then
create a new GitHub Issue to discuss with the team:

1. The scorecard must only be composed of automate-able, objective data. For
    example, a project having 10 contributors doesn’t necessarily mean it’s more
    secure than a project with 50 contributors. But, having two maintainers
    might be preferable to only having one - the larger bus factor and ability
    to provide code reviews is objectively better.
2. The scorecard criteria can be as specific as possible and are not limited to
    general recommendations. For example, for Go, we can recommend/require
    specific linters and analyzers to be run on the codebase.
3. The scorecard can be populated for any open source project without any work
    or interaction from maintainers.
4. Maintainers must be provided with a mechanism to correct any automated
    scorecard findings they feel were made in error, provide "hints" for
    anything we can't detect automatically, and even dispute the applicability
    of a given scorecard finding for that repository.
5. Any criteria in the scorecard must be actionable. It should be possible,
    with help, for any project to "check all the boxes".
 Any solution to compile a scorecard should be usable by the greater open
    source community to monitor upstream security.

=======

## Check Brainstorm

### SBOM Check

- Description: Establish framework to check for and assign points to sbom related items, beginning with existence of published sbom in any form, leveraging the existing and emerging standards for locations and naming. Also with the ability to add additional functionality later as sboms become more adopted.
- Considerations:
  - Discussion in original SBOM Check issue (<https://github.com/ossf/scorecard/issues/1476>) talks about having sbom check as a "nice to have" check that won't affect existing scores, link (<https://github.com/ossf/scorecard/issues/1476#issuecomment-1442452128>)
    - David Wheeler is against any optional checks (find notes from working group meeting)
    - One option is to use the `Info:` logging option to inform users about missing sbom.
  - Determine method for checking if project type should have an SBOM? (Arbitrary, but Maintainers Annotation may provide means for maintainers to make their project exempt from this check)
- Probes:
  - Check for published SBOM (MVP)
  - Check for source Sbom (top level)
  - Validate SBOM format (spdx, cyclonedx) (MVP?) (sbom-scorecard handles this, We should let #2605 handle this portion)
  - Check for SBOM quality (leveraging sbom-scorecard) (#2605)
- Initial Point Assignment: (Purposed)
  - Published Sbom exists in release artifacts: 7 points
  - sbom exists in repo (source or artifacts): 3 points
- Future Point Assignment:
  - Published Sbom exists: 3 points
  - sbom exists in repo (source or artifacts): 3 points
  - sbom-scorecard "quality" result (result percentage * 4)

#### Development Notes

Top-Level sbom Check Tests:

- no sbom (no points)
- published sbom (full points) (Not exactly sure how to test this)
- source sbom (partial points)

### Probe Breakdown - Check for published SBOM

- Description: Check locations/files set down in associated standards for an sbom for the project.
- Challenges:
  - Unfamiliar with github releases/api/workflows.
  - Need to determine artifact/job name to determine potential sbom name
- Considerations:
  - In what order should the locations be checked? Does it matter?
  - Check for SLSA Provenances in release pipeline
  - Should a certain # of releases be checked in order to be awarded points?
- Standards to leverage:
  - security insights 1.0.0 spec:
    - <https://github.com/ossf/security-insights-spec/blob/v1.0.0/specification.md#dependencies>
  - sbom-everywhere naming and directory conventions:
    - <https://github.com/ossf/sbom-everywhere/blob/main/reference/sbom_naming.md>
- places to check:
  - Git(lab/hub) release artifacts
  - pipeline/workflow artifacts
  - sbom-everywhere naming and directory conventions:

    | Standard  | Format    | Artifact Filename     | SBOM Filename |
    |:----------|:----------|:----------------------|:--------------|
    | CycloneDX | JSON      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.cdx.json |
    | CycloneDX | XML       | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.cdx.xml |
    | SPDX      | TAG:VALUE | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx |
    | SPDX      | JSON      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.json |
    | SPDX      | XML       | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.xml |
    | SPDX      | YAML      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.yml (or .yaml) |
    | SPDX      | RDF XML   | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.rdf (or .rdf.xml) |

### Probe Breakdown - Check for Source Sbom

- Challenges:
  - Need to determine artifact/job name to determine potential sbom name
- Standards to leverage:
  - security insights 1.0.0 spec:
    - <https://github.com/ossf/security-insights-spec/blob/v1.0.0/specification.md#dependencies>
  - sbom-everywhere naming and directory conventions:
    - <https://github.com/ossf/sbom-everywhere/blob/main/reference/sbom_naming.md>
- places to check:
  - root of repo directory
  - boms/sboms (hidden folders? .bom/.sbom)
    - security insights 1.0.0 spec:
      - Location specified in SECURITY_INSIGHTS.yml (dependencies#sbom section)
    - sbom-everywhere naming and directory conventions:

    | Standard  | Format    | Artifact Filename     | SBOM Filename |
    |:----------|:----------|:----------------------|:--------------|
    | CycloneDX | JSON      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.cdx.json |
    | CycloneDX | XML       | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.cdx.xml |
    | SPDX      | TAG:VALUE | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx |
    | SPDX      | JSON      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.json |
    | SPDX      | XML       | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.xml |
    | SPDX      | YAML      | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.yml (or .yaml) |
    | SPDX      | RDF XML   | artifact-1.0.0.tar.gz | artifact-1.0.0.tar.gz.spdx.rdf (or .rdf.xml) |
