# Hyperledger Fabric binaries for AArch64/ARM64 (Raspberry Pi 2/3/4)

This repository provides precompiled Fabric binaries (based on Fabric v2.1) for AArch64/ARM64 that were created as part of a project at Linköping University. They may contain issues and will not be maintained.

### Binary downloads can be found in the `bin/` folder of the releases available [here](https://github.com/busan15/fabric-binaries-pi/releases). Docker images are also available at https://hub.docker.com/u/busan15.

The binaries are distributed with a copy of https://github.com/hyperledger/fabric-samples that has been modified to use the ARM docker images.


## How to setup Hyperledger Fabric on a Raspberry Pi

Using the binaries and modified fabric-samples provided here, getting Fabric to run on a Raspberry Pi or other AArch64-powered systems can be done using the following steps:

1. Ensure that the target processor has support for AArch64. Recent models of the Raspberry Pi 2 (revision 1.2 and newer using the BCM2837 CPU), all revisions of the Raspberry Pi 3 and Raspberry Pi 4 should be compatible. This project was tested on a Raspberry Pi 4 4GB rev. 1.1. Fabric does not compile on 32-bit architectures due to integer overflows, which excludes the older Raspberry Pi 1, Raspberry Pi 2 rev. 1.1, and all current Raspberry Pi Zero models, since they lack 64-bit support. While it is possible that the Fabric code could be modified to work on 32-bit systems, this was out of scope for this project.
2. Install a 64-bit operating system. While several Raspberry Pi models have 64-bit support, many operating systems (including Raspbian) still uses 32-bit kernels. For this project, [Ubuntu](https://ubuntu.com/download/raspberry-pi) was used as it has a large set of packages available for AArch64.
3. Install Docker as specified in the [Docker documentation](https://docs.docker.com/engine/install/ubuntu/). If you are familiar with Docker and don't want to use the fabric-samples provided by Hyperledger, stop here and use the Docker images available [here](https://hub.docker.com/u/busan15).
4. Download the binaries available in the [release section of this repository](https://github.com/busan15/fabric-binaries-pi/releases) and unpack the compressed archive. The steps outlined in [Hyperledger's instructions for setting up a test network](https://hyperledger-fabric.readthedocs.io/en/release-2.1/test_network.html) should now work as detailed below.
5. Change to the test-network directory by running `cd fabric-binaries-pi/test-network`.
6. Start the network with `./network.sh up`.
7. Create a blockchain channel with `./network.sh createChannel`.
8. Deploy the sample chaincode with `./network.sh deployCC`. This command sometimes fails with the message `Error: failed to endorse chaincode install: rpc error: code = Unavailable desc = transport is closing`, although the chaincode seems to be successfully deployed after rerunning the command a total of three times.
9. If everything worked correctly, the output should include something like `[{"Key":"CAR0","Record":{"make":"Toyota" [...]`.
10. To futher [interact with the network](https://hyperledger-fabric.readthedocs.io/en/release-2.1/test_network.html#interacting-with-the-network), it might be necessary to specify the location of a configtx.yaml file. This can be achived through the command `export FABRIC_CFG_PATH=$PWD/../config/`, which uses the sample file provided with fabric-samples.