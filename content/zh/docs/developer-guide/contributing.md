---
title: "参与贡献"
weight: 3
---

Welcome to KubeCube community! If you are looking for information on how to join us, you are in the right place. We encourage you to help out by reporting issues, improving documentation, fixing bugs, or adding new features. Every contribution is precious for KubeCube.

## Reporting issues
To be honest, we regard every user of KubeCube as a very kind contributor. After experiencing KubeCube, you may have some feedback for the project. Then feel free to open an issue.

There are lot of cases when you could open an issue:

bug report
feature request
performance issues
feature proposal
feature design
help wanted
doc incomplete
test improvement
any questions on project
and so on

Also we must remind that when filing a new issue, please remember to remove the sensitive data from your post. Sensitive data could be password, secret key, network locations, private business data and so on.

## Contribution

To put forward a PR, we assume you have registered a GitHub ID. Then you could finish the preparation in the following steps:

1. Fork the repository you wish to work on. You just need to click the button Fork in right-left of project repository main page. Then you will end up with your repository in your GitHub username.
2. Clone your own repository to develop locally. Use git clone https://github.com/<your-username>/<project>.git to clone repository to your local machine. Then you can create new branches to finish the change you wish to make.
3. Set remote upstream to be https://github.com/kubecube-io/<project>.git using the following two commands:

```bash
git remote add upstream https://github.com/kubecube-io/<project>.git
git remote set-url --push upstream no-pushing
```

Adding this, we can easily synchronize local branches with upstream branches.

4. **Create a branch** to add a new feature or fix issues

Update local working directory:

```bash
cd <project>
git fetch upstream
git checkout master
git rebase upstream/master
```

Create a new branch:

```bash
git checkout -b <new-branch>
```

Make any change on the new-branch then build and test your codes.

### PR Description

PR is the only way to make changes to KubeCube project files. To help reviewers better get your purpose, PR description could not be too detailed. We encourage contributors to follow the [PR template](https://github.com/kubecube-io/kubecube/blob/main/.github/PULL_REQUEST_TEMPLATE.md) to finish the pull request.

### Developing Environment

As a contributor, if you want to make any contribution to KubeCube project, we should reach an agreement on the version of tools used in the development environment. Here are some dependents with specific version:

Golang : v1.14+ (1.16 is best)
Kubernetes: v1.18+

### Developing guide

There's a `Makefile` in the root folder which describes the options to build and install. Here are some common ones:

```bash
# Run the tests
make test

# Swag doc generate
make swag-gen

# Cube binary build
make docker-build-cube

# Warden binary build
make docker-build-warden

# Install dependence into env
make install
```

If you want to start KubeCube and Warden locally to work with a Kubernetes cluster, you should follow the [debug guide](https://www.kubecube.io/docs/developer-guide/debug/).

## Join KubeCube as a member

It is also welcomed to join KubeCube team if you are willing to participate in KubeCube community continuously and keep active.

### Requirements

- Have read the [Contributing to KubeCube](https://www.kubecube.io/docs/developer-guide/contributing/) carefully
- Have submitted multi PRs to the community
- Be active in the community, may including but not limited
  - Submitting or commenting on issues
  - Contributing PRs to the community
  - Reviewing PRs in the community

### How to do it

You can try in either of two ways:

- Report a issues

- Submit a PR in the project repo

  