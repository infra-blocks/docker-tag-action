# docker-tag-action
[![Release](https://github.com/infra-blocks/docker-tag-action/actions/workflows/git-tag-semver-from-label.yml/badge.svg)](https://github.com/infra-blocks/docker-tag-action/actions/workflows/git-tag-semver-from-label.yml)
[![Self Test](https://github.com/infra-blocks/docker-tag-action/actions/workflows/self-test.yml/badge.svg)](https://github.com/infra-blocks/docker-tag-action/actions/workflows/self-test.yml)
[![Update From Template](https://github.com/infra-blocks/docker-tag-action/actions/workflows/update-from-template.yml/badge.svg)](https://github.com/infra-blocks/docker-tag-action/actions/workflows/update-from-template.yml)

An action that tags a provided docker image. This action uses the Docker CLI but does not log into a registry.
This is expected to happen before calling this action.

Because we are using the CLI, we may have to pull the provided image first, so the action should also have the
necessary privileges. We first check the local docker cache to see if the image exists locally. If not, we pull.

The action returns the published images with their respective tags.

## Inputs

|  Name   | Required | Description                                                                                                                       |
|:-------:|:--------:|-----------------------------------------------------------------------------------------------------------------------------------|
|  image  |   true   | The docker image to tag. This should include the tag or the corresponding digest. If no tag is provided, the default is "latest". |
|  tags   |   true   | A stringified JSON array of tags to apply on the built image. Defaults to '["latest"]'                                            |
| dry-run |  false   | Whether to publish the changes or not.                                                                                            |

## Outputs

|    Name    | Description                                                              |
|:----------:|--------------------------------------------------------------------------|
| repository | The extracted repository from the image provided.                        |
| published  | The stringified JSON array of images corresponding to the tags provided. |

## Permissions

N/A

## Usage

### Example with ECR Public
```yaml

steps:
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: ${{ vars.AWS_ROLE }}
      aws-region: ${{ vars.AWS_REGION }}
  - name: Login to Amazon ECR Public
    uses: aws-actions/amazon-ecr-login@v2
    with:
      registry-type: public
  - name: Tag images
    id: docker-tag
    uses: infra-blocks/docker-tag-action@v1
    with:
      image: public.ecr.aws/<your-org>/<your-repo>:12345
      tags: '["v1", "latest", "big-change"]'
  - run: |
      # Will output: 
      # '["public.ecr.aws/<your-org>/<your-repo>:v1","public.ecr.aws/<your-org>/<your-repo>:latest","public.ecr.aws/<your-org>/<your-repo>:big-change"]'
      echo "${{ steps.outputs.published }}"
```
