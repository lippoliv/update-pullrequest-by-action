# Update Pull Request by GitHub Actions

In one of my projects, it was useful to have a specific label, and a specific reviewer set automatically for all pull
requests towards main branch.

So the question came up: **Can we have an workflow triggered by opening pull requests towards main branch?**

## Run GitHub Action workflow on new pull request towards main branch

In GitHub it's quiet easy to run workflows on pull request creation to some specific branch:

```shell
on:
  pull_request:
    branches:
      - 'main'
    types:
      - opened
```

This defines to run this workflow on pull requests towards main branch and just in the moment of PR creation.

## Set label for pull request by GitHub Action

In the [add label](https://github.com/lippoliv/update-pullrequest-by-action/blob/main/.github/workflows/new-pr.yml#L14)
step you see, that you can read out the pull request url and then send a simple post request to the API

```shell
    export PR_URL=$(cat "$GITHUB_EVENT_PATH" | jq '.pull_request._links.issue.href' | cut -d '"' -f 2)
    curl --request POST \
        --url $PR_URL \
        --header 'Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}' \
        --header 'Content-Type: application/json' \
        --data '{
          "labels": ["enhancement"]
        }' \
        --fail
```

## Set label for pull request by GitHub Action

In
the [add reviewer](https://github.com/lippoliv/update-pullrequest-by-action/blob/main/.github/workflows/new-pr.yml#L29)
step you see, that you can read out the pull request reviewers url and then send a simple post request to the API

```shell
    export PR_URL=$(cat "$GITHUB_EVENT_PATH" | jq '.pull_request._links.self.href' | cut -d '"' -f 2)
    curl --request POST \
        --url $PR_URL/requested_reviewers \
        --header 'Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}' \
        --header 'Content-Type: application/json' \
        --data '{
          "reviewers": ["some-user"]
        }' \
        --fail
```
