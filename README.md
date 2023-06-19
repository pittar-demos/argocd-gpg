# Argo CD: Signed Commits Demo

A demo of Argo CD checking for commit signatures!

## Create a GPG Key

If you already have a GPG key locally, you can skip this step.  Otherewise, [follow these instructions to create a new one]().

## List your GPG Key and Display Public Key

First, list your keys:

```
gpg --list-secret-keys --keyid-format=long
```

Then export the key you wish to use:

```
gpg --armor --export 3AA5C34371567BD2
```

You will need to copy the entire public key (including the `-----BEGIN` and `-----END` lines) to add to Argo CD and to your GitHub account.

## Add Your Public Key to GitHub

If you want GitHub to show that your commits have been signed and verified, you will need to add your public key to your GitHub account.

To do so, [follow these GitHub instructions](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account).

## Add Your Public Key to Argo CD

As an Argo CD "admin", you can add your public GPG key to Argo CD either [using the Agoo CD cli](https://argo-cd.readthedocs.io/en/stable/user-guide/gpg-verification/#manage-public-keys-using-the-cli) or [using the Argo CD UI](https://argo-cd.readthedocs.io/en/stable/user-guide/gpg-verification/#manage-public-keys-using-the-web-ui).

## Configuring your AppProject

The last step is to include the list of GPG key IDs in your `AppProject` definition.  These IDs should match the GPG key(s) you have already uploaded that you want to use to verify commits `Applications` associated with this `AppProject`.

As part of your `AppProject`, include the GPG key ID(s) you want to use:

```
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: gpg
  namespace: argocd
spec:
  clusterResourceWhitelist:
  - group: '*'
    kind: '*'
  description: GnuPG verification
  destinations:
  - namespace: '*'
    server: '*'
  namespaceResourceWhitelist:
  - group: '*'
    kind: '*'
  signatureKeys:
  - keyID: 3AA5C34371567BD2
  sourceRepos:
  - '*'
```

## Sample App

You can deploy the sample app in this repository.  You can see if the last commit is signed or not by viewing the commit history on GitHub.  If the last commit is signed, then the sync should work.

## Testing

From now on, Argo CD will verify the GPG signature of a commit before it will sync.  You can easily test this:

1. Commit (unsigned) and Push
2. "Sync" your Application in Argo CD
3. You should have a Sync error due to signature verification failure.

You can fix this by doing the following:

1. Commit (signed) and Push
2. "Sync" your Application in Argo CD
3. You should have a successful (green) sync! 

