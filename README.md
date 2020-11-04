# Hyperledger Fabric binaries for AArch64/ARM64 (Raspberry Pi 2/3/4)

This repository provides precompiled Fabric binaries (based on Fabric v2.1) for AArch64/ARM64 that were created as part of a project at Linköping University. They may contain issues and will not be maintained.

### Binary downloads can be found in the `bin/` folder of the releases available [here](https://github.com/busan15/fabric-binaries-pi/releases). Docker images are also available at https://hub.docker.com/u/busan15.

The binaries are distributed with a copy of https://github.com/hyperledger/fabric-samples that has been modified to use the ARM docker images.

## Compiling the images

There are also a set of patch files in this repository that contains the changes made to the source code of fabric, fabric-baseimage, and fabric-ca to make these compile on ARM They likely have to be applied to the exact commit they were made for to avoid conflicts though.

These are:

* fabric: f7f505bd76ae72526c17a255f970a0114dea19b6
(https://github.com/hyperledger/fabric/tree/f7f505bd76ae72526c17a255f970a0114dea19b6)
* fabric-baseimage: 12718aaae2e45f4c69043073292c9ff8850bbc57 (https://github.com/hyperledger/fabric-baseimage/tree/12718aaae2e45f4c69043073292c9ff8850bbc57)
* fabric-ca: 34c5a3c5223e461e8a239e30bd4420026da2fd0a (https://github.com/hyperledger/fabric-ca/tree/34c5a3c5223e461e8a239e30bd4420026da2fd0a)

To apply these changes and also update to a later version, you could probably (for each of these three) clone the repository, checkout the commit specified above, apply the corresponding patch with `git apply <patch file>`, commit or stash the changes, and then update with `git pull`. This might cause conflicts, so if you are happy with an older version you could also stop after applying the patches and compile from there.

## How to setup Hyperledger Fabric on a Raspberry Pi (using the precompiled images)

Using the binaries and modified fabric-samples provided here, getting Fabric to run on a Raspberry Pi or other AArch64-powered systems can be done using the following steps:

1. Ensure that the target processor has support for AArch64. Recent models of the Raspberry Pi 2 (revision 1.2 and newer using the BCM2837 CPU), all revisions of the Raspberry Pi 3 and Raspberry Pi 4 should be compatible. This project was tested on a Raspberry Pi 4 4GB rev. 1.1. Fabric does not compile on 32-bit architectures due to integer overflows, which excludes the older Raspberry Pi 1, Raspberry Pi 2 rev. 1.1, and all current Raspberry Pi Zero models, since they lack 64-bit support. While it is possible that the Fabric code could be modified to work on 32-bit systems, this was out of scope for this project.
2. Install a 64-bit operating system. While several Raspberry Pi models have 64-bit support, many operating systems (including Raspbian) still uses 32-bit kernels. For this project, [Ubuntu](https://ubuntu.com/download/raspberry-pi) was used as it has a large set of packages available for AArch64.
3. Install Docker as specified in the [Docker documentation](https://docs.docker.com/engine/install/ubuntu/). If you are familiar with Docker and don't want to use the fabric-samples provided by Hyperledger, stop here and use the Docker images available [here](https://hub.docker.com/u/busan15).
4. Install golang and docker-compose with `sudo apt install golang docker-compose`.
5. Add the "ubuntu" user to the Docker group with `sudo usermod -aG docker ubuntu` followed by logging out and back in again
6. Download the binaries available in the [release section of this repository](https://github.com/busan15/fabric-binaries-pi/releases) and unpack the compressed archive. The steps outlined in [Hyperledger's instructions for setting up a test network](https://hyperledger-fabric.readthedocs.io/en/release-2.1/test_network.html) should now work as detailed below.
7. As a workaround to an issue (#1) where a non-arm64 compatible Docker image is being referenced by Fabric, download the fabric-baseos image manually with `docker pull busan15/fabric-baseos:2.1` and tag it with `docker tag busan15/fabric-baseos:2.1 hyperledger/fabric-baseos:2.1` to force the use of the arm64 image.
8. Change to the test-network directory by running `cd fabric-samples/test-network`.
9. Start the network with `./network.sh up`.
10. Create a blockchain channel with `./network.sh createChannel`.
11. Deploy the sample chaincode with `./network.sh deployCC`. This command sometimes fails with the message `Error: failed to endorse chaincode install: rpc error: code = Unavailable desc = transport is closing`, although the chaincode seems to be successfully deployed after rerunning the command a total of three times.
12. If everything worked correctly, the output should include something like `[{"Key":"CAR0","Record":{"make":"Toyota" [...]`.
13. To futher [interact with the network](https://hyperledger-fabric.readthedocs.io/en/release-2.1/test_network.html#interacting-with-the-network), it might be necessary to specify the location of a configtx.yaml file. This can be achived through the command `export FABRIC_CFG_PATH=$PWD/../config/`, which uses the sample file provided with fabric-samples.