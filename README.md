# docker-tag-action

An action that tags a provided docker image. This action uses the Docker CLI but does not log into a registry.
This is expected to happen before calling this action.

Because we are using the CLI, we have to pull the provided image first, so the action should also have the necessary
privileges.

The action returns the published images with their respective tags.

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
    uses: infrastructure-blocks/docker-tag-action@v1
    with:
      image: public.ecr.aws/<your-org>/<your-repo>:12345
      tags: '["v1", "latest", "big-change"]'
  - run: |
      # Will output: 
      # '["public.ecr.aws/<your-org>/<your-repo>:v1","public.ecr.aws/<your-org>/<your-repo>:latest","public.ecr.aws/<your-org>/<your-repo>:big-change"]'
      echo "${{ steps.outputs.published }}"
```

## Development

### Releasing

The releasing is handled at git level with semantic versioning tags. Those are automatically generated and managed
by the [git-tag-semver-from-label-workflow](https://github.com/infrastructure-blocks/git-tag-semver-from-label-workflow).
