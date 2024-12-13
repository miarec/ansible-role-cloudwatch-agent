# Molecule test this role

## Run test
This will test the role installing a specific version of rtpengine from source code.

Run Molecule test

```
molecule test
```

Run test with variable example

```
MOLECULE_DISTRO=ubuntu2204 molecule test
```

## Variables

 - `MOLECULE_DISTRO` OS of docker container to test, default `ubuntu2204`
    List of tested distros:
    - `ubuntu2204`
    - `ubuntu2004`

 - `MOLECULE_ANSIBLE_VERBOSITY` 0-3 used for troubleshooting, will set verbosity of ansible output, same as `-vvv`, default `0`
