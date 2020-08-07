# metadata-env-buildkite-plugin [![Build status](https://badge.buildkite.com/8b75eb87da8457a67c926458f45aac395a02a9f49994dd767b.svg?branch=master)](https://buildkite.com/kuda/metadata-env-buildkite-plugin)

Buildkite plugin to inject buildkite-agent metadata into environment

Due to limitations of the plugin, the environment variables generated from the plugin can only be accessed during runtime

It is recommended that these environment variables are accessed by escaping the `$` character

```
\$FOO or $$FOO
```

https://buildkite.com/docs/agent/v3/cli-pipeline#environment-variable-substitution

This is particularly useful as **block step** values are saved in a build's **meta-data**.
https://buildkite.com/docs/pipelines/block-step
https://buildkite.com/docs/agent/v3/cli-meta-data

## Example

### Simple
```yml
steps:
  - label: "Setting meta-data"
    commands:
      - 'buildkite-agent meta-data set "hello" "world"'
  - label: "Fetching meta-data"
    commands:
      - test $$HELLO = world
    plugins:
      - chronotc/metadata-env#v1.0.0:
          keys:
            - hello=HELLO
```

### Docker
```yml
steps:
  - label: "Setting meta data"
    commands:
      - 'buildkite-agent meta-data set "foo" "bar"'
  - label: "Fetching meta-data for container"
    commands:
      - test $$FOO = bar
    plugins:
      - chronotc/metadata-env#v1.0.0:
          keys:
            - foo=FOO
      - docker#v3.5.0:
          image: "alpine:3.7"
          environment:
            - FOO
```

### Block step

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
  - command: echo $$NODE_ENV
    plugins:
      - chronotc/metadata-env#v1.0.0:
          keys:
            - ROLE
            - node-env=NODE_ENV #remaps node-env key to NODE_ENV <key>=<alias>
```