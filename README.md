![Reviewabot Banner](docs/images/reviewabot-banner.png)

# Reviewabot Action

Reviewabot automates the process of creating a pull request review. It uses generative AI to create a review based on your instructions.

Read more about Reviewabot in [this article on medium](https://medium.com/@simon.c.kofod/reviewabot-your-ai-pull-request-reviewer-cd11b46aeca2), or have a look at the [Reviewabot source code](https://github.com/simon-k/reviewabot).

Here is an example of a generated PR Review.

![Reviewabot Banner](docs/images/review-example.png)

## Requirements
### OpenAI API Key or Azure OpenAI API Key
You need either an OpenAI API key or an Azure OpenAI API key to generate reviews. 

#### OpenAI API Key 

You can create an API key by visiting the [OpenAI website](https://platform.openai.com/account/api-keys).

___Place the API Key in a repository secret named `OPENAI_KEY`.___

#### Azure OpenAI API Key
You can create an Azure OpenAI API key by visiting the [Azure Portal](https://portal.azure.com/). Create an OpenAI resource and deploy the GPT-4o model. Get the API key and the endpoint.

___Place the API Key in a repository secret named `AZURE_OPENAI_KEY`.___

___Place the API Endpoint in a repository secret named `AZURE_OPENAI_ENDPOINT`.___

### GitHub Personal Access Token
A review must be created by a GitHub user, so you will need a GitHub Personal Access Token (PAT) with permission to read source and write reviews from a GitHub User Account that has been added as a contributor to the repository.

Since you cannot review your own code, I suggest that you create a new GitHub user account for the reviewer.

___Place the PAT of the reviewer in a repository secret named `REVIEWER_GITHUB_PAT`.___

## Inputs

| Input Name               | Description                                                                          | Required | Default                             |
|--------------------------|--------------------------------------------------------------------------------------|----------|-------------------------------------|
| `reviewer-pat`           | The reviewer's Private Access Token with permission to read source and write reviews | true     |                                     |
| `open-ai-key`            | Your OpenAI Key                                                                      | false    |                                     |
| `azure-open-ai-key`      | Your Azure OpenAI Key                                                                | false    |                                     |
| `azure-open-ai-endpoint` | Your Azure OpenAI Endpoint                                                           | false    |                                     |
| `owner`                  | Name of the owner of the repository under review                                     | false    | `${{github.repository_owner}}`      |
| `repository`             | Name of the repository under review                                                  | false    | `${{github.event.repository.name}}` |
| `pr-number`              | The pull request number                                                              | false    | `${{github.event.number}}`          |

You must provide either an OpenAI key or an Azure OpenAI key together with an Azure OpenAI endpoint.

## Example Usage

Here is an example that will trigger a review every time a pull request is opened.

```yaml
name: Review Pull Request
on:
  pull_request:
    types: [opened]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
    - name: Run Reviewabot
      uses: Reviewabot/action@v3.0.0
      with:
        open-ai-key: ${{ secrets.OPENAI_KEY }}
        reviewer-pat: ${{ secrets.REVIEWER_GITHUB_PAT }}
```

Here is an example that will trigger a review every time a given reviewer is assigned.

IMPORTANT: Insert the name of your reviewer in the action at the line containing the condition.

```yaml
name: Review Pull Request

on:
  pull_request_target:
    types: [review_requested]
    
jobs:
  review:
    if: ${{ github.event.requested_reviewer.login == '[THE NAME OF YOUR REVIEWER]' }}
    runs-on: ubuntu-latest 
    steps:
    - name: Run Reviewabot
      uses: Reviewabot/action@v3.0.0
      with:
        open-ai-key: ${{ secrets.OPENAI_KEY }}
        reviewer-pat: ${{ secrets.REVIEWER_GITHUB_PAT }}
```

Here is an example where the owner, repository, and pull request number are specified. The review runs every time a PR is opened.

```yaml
name: Review Pull Request

on:
  pull_request:
    types: [opened]
    
jobs:
  review:
    runs-on: ubuntu-latest 
    steps:
    - name: Run Reviewabot
      uses: Reviewabot/action@v3.0.0
      with:
        open-ai-key: ${{ secrets.OPENAI_KEY }}
        reviewer-pat: ${{ secrets.REVIEWER_GITHUB_PAT }}
        owner: 'my-username'
        repository: 'my-repo'
        pr-number: '1'
```
