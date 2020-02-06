# Repository Dispatch
A GitHub action to create a repository dispatch event.

## Usage

```yml
      - name: Repository Dispatch
        uses: broadinstitute/repository-dispatch@v1
        with:
          token: ${{ secrets.REPO_ACCESS_TOKEN }}
          event-type: my-event
```

## Parameters

- `token` (**required**) - A `repo` scoped GitHub [Personal Access Token](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line).
- `repository` - The full name of the repository to send the dispatch. Defaults to the current repository.
- `event-type` (**required**) - A custom webhook event name.
- `client-payload` - JSON payload with extra information about the webhook event that your action or worklow may use. Default: {}

## Example

Here is an example setting all of the input parameters.

```yml
      - name: Repository Dispatch
        uses: broadinstitute/repository-dispatch@1.0.0
        with:
          token: ${{ secrets.REPO_ACCESS_TOKEN }}
          repository: username/my-repo
          event-type: my-event
          client-payload: '{"ref": "${{ github.ref }}", "sha": "${{ github.sha }}"}'
```

Here is an example `on: repository_dispatch` workflow to receive the event.

```yml
name: Repository Dispatch
on:
  repository_dispatch:
    types: [my-event]
jobs:
  myEvent:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          ref: ${{ github.event.client_payload.ref }}
      - run: echo ${{ github.event.client_payload.sha }}
```

## Client payload

The GitHub API allows a maximum of 10 top-level properties in the `client-payload` JSON.
If you use more than that you will see an error message like the following.

```
No more than 10 properties are allowed; 14 were supplied.
```

For example, this payload will fail because it has more than 10 top-level properties.

```yml
client-payload: ${{ toJson(github) }}
```

To solve this you can simply wrap the payload in a single top-level property.
The following payload will succeed.

```yml
client-payload: '{"github": ${{ toJson(github) }}}'
```

## License

[MIT](LICENSE)
