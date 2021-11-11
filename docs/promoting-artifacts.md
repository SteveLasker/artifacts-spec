# Promoting Artifacts

DevOps workflows account for content promotion between environments. Content may be promoted from dev through staging to production. Or, it may be promoted from a public source into a controlled environment.

Consider the guidance for [Consuming Public Content][oci-consuming-public-content], where content required for internal use is imported from a public registry. How is this different from promoting content from dev through production?

## What Gets Promoted

ORAS Artifacts enables a graph of content, including signatures, SBoMs, security scan results and other content that hasn't been thought of yet.

A key value of separable artifacts is the ability to validate content, independent from where it may originate from. Before importing a container image, a signature and/or SBoM may be validated. Only after the validation is the image imported, or promoted.

When import is promoted, the entire graph of artifacts would likely be imported as the next stage of the promotion should be able to validate independent of any previous steps.

At the same time, not all referenced artifacts may be desired. A category of `artifactTypes` or specific artifacts within a category may need to be filtered out, or filtered in.

1. Import the nginx image from docker hub, including the associated SBoMs and signatures. However, the released notes, gpl source or license info may not be wanted.
1. Promote the nginx image from staging through production. The SBoM, Scan Results and upstream signatures have all been pre-validated. The only referenced artifact required in production is the ACME Rockets signature. 

## Promoting Content Walkthrough

- Copy with all references
  ```bash
  oras copy registry.wabbit-networks.io/net-monitor:v1 \
    --destination registry.acme-rockets.io/library/net-monitor:v1 \
    --source-user $SOURCE_USER \
    --source-pwd $SOURCE_PWD \
    --destination-user $DESTINATION_USER \
    --destination-pwd $DESTINATION_PWD \
    --include-references true 
  ```
  > NOTE: source and destination authentication can leverage the auth stores. These are optional auth parameters when not stored
- Copy with specific references, included
  ```bash
  oras copy registry.wabbit-networks.io/net-monitor:v1 \
    --destination registry.acme-rockets.io/library/net-monitor:v1 \
    --source-user $SOURCE_USER \
    --source-pwd $SOURCE_PWD \
    --destination-user $DESTINATION_USER \
    --destination-pwd $DESTINATION_PWD \
    --include-references true \
    --artifact-type application/vnd.cncf.notary.v2 \
    --artifact-type sbom/example
  ```

- Copy with specific references, excluded
  ```bash
  oras copy registry.wabbit-networks.io/net-monitor:v1 \
    --destination registry.acme-rockets.io/library/net-monitor:v1 \
    --source-user $SOURCE_USER \
    --source-pwd $SOURCE_PWD \
    --destination-user $DESTINATION_USER \
    --destination-pwd $DESTINATION_PWD \
    --exclude-references true \
    --artifact-type sbom/example
  ```

- Copy with specific references, included, filtered by annotation
  ```bash
  oras copy registry.wabbit-networks.io/net-monitor:v1 \
    --destination registry.acme-rockets.io/library/net-monitor:v1 \
    --source-user $SOURCE_USER \
    --source-pwd $SOURCE_PWD \
    --destination-user $DESTINATION_USER \
    --destination-pwd $DESTINATION_PWD \
    --include-references true \
    --artifact-type application/vnd.cncf.notary.v2 \
    --annotation "org.cncf.notary.v2.signature.subject=wabbit-networks.io"
  ```


[oci-consuming-public-content]:         https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
