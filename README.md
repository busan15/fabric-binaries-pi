# Hyperledger Fabric binaries for AArch64/ARM64 (Raspberry Pi 2/3/4)

This repository provides precompiled Fabric binaries for AArch64/ARM64 that were created as part of a project at Linköping University. They may contain issues and will not be maintained.

### Binary downloads can be found in the `bin/` folder of the releases available [here](https://github.com/busan15/fabric-binaries-pi/releases). Docker images are also available at https://hub.docker.com/u/busan15.

The binaries are distributed with a copy of https://github.com/hyperledger/fabric-samples that has been modified to use the ARM docker images.

When following the [instructions for setting up a test network](https://hyperledger-fabric.readthedocs.io/en/release-2.0/test_network.html), the `./network.sh deployCC` command fails with the message `Error: failed to endorse chaincode install: rpc error: code = Unavailable desc = transport is closing`, although the chaincode seems to be successfully deployed after rerunning `./network.sh deployCC` a total of three times.