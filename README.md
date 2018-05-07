# metadata-env-buildkite-plugin

Buildkite plugin to inject buildkite-agent metadata into environment

This is particularly useful as **block step** values are saved in a build's **meta-data**.

https://buildkite.com/docs/pipelines/block-step


## Example

```yml
steps:
  - block: "Request Release"
    fields:
      - select: "Select account"
        key: ROLE # saves to buildkite-agent meta-data
        options:
          - label: "Production"
            value: "arn:aws:iam::123456789:role/production-role"
          - label: "Staging"
            value: "arn:aws:iam::987654321:role/staging-role"
      - select: "Select runtime environment"
        key: node-env # saves to buildkite-agent meta-data
        options:
          - label: "Production"
            value: "production"
          - label: "Development"
            value: "development"
  - command: echo \$NODE_ENV
    plugins:
      ACloudGuru/metadata-env:
        keys:
          - ROLE
          - node-env=NODE_ENV #remaps node-env key to NODE_ENV <key>=<alias>
```