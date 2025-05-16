# BOUT-spack

Aim
1. Have hermes-3 tests run out-of-the-box when you activate a spack environment with hermes-3 installed
2. Facilitate H3 development in a spack environment

For 1., need to install xhermes via spack, see this [BOUT-spack PR](https://github.com/boutproject/BOUT-spack/pull/2)

## Possible dev workflows

- Integrated env
  1. Make BOUT-spack a git submodule of H3
  2. Include a spack env file (spack.yaml) in the H3 repo - could merge with the one in ./docker and have both methods use it?
  3. Could also use checked-out H3 source (rather than letting spack git-clone a particular version) - would handle variants, any changes in dependencies as you develop (same as NEPTUNE approach).
- Separate env, same config for everybody
  1. Create a 'hermes-spack-env' repo; contains BOUT-spack as a submodule and a spack.yaml
  2. Users clone the repo, activate the environment, then call cmake from that environment, pointing it to separate H3 source
- Separate env, per-user config
  1. Users clones BOUT-spack, adds packages to a spack env, installs globally as required.
