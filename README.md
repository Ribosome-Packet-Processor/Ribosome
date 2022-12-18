# Ribosome
Ribosome is a system that extends programmable switches with external memory (to store packets) and external general-purpose packet processing devices such as CPUs or FPGAs (to perform stateful operations). As today's packet processing devices are bottlenecked by their network interface speeds, Ribosome carefully transmits only the relevant bits to these devices. Ribosome leverages the spare bandwidth from any directly connected server to store the incoming payloads through RDMA.

This implementation of Ribosome leverages on RDMA servers as remote buffers.

For more details see the [original paper](https://compunet.ing.uniroma3.it/assets/publications/RibosomeNSDI2023.pdf).

## Overview
The following figure shows an overview of the Ribosome system.

![image](https://user-images.githubusercontent.com/26346836/193598837-ac7d7176-8410-4445-b94f-ec77a2a8ca91.png)

The programmable switch, running the Ribosome-P4 implementation, receives incoming packets (step 1) and splits each packet whose size is above a predefined threshold into a small header and a larger payload chunk. The programmable switch assigns an ID to both the header and the payload chunks. The switch assigns increasing IDs to each received packet within a predefined range (in a modulo manner). The switch forwards the header of the packet to one of the external NF packet processors (step 2a) and the payload to one of the shared servers (chosen hashing the flow 5-tuple) using RDMA (step 2b). The NF packet processors store the per-flow state needed to process any incoming packets. The NF uses this state to transform each incoming header into a new processed header, which is sent back to the programmable switch where it is stored on its small memory using the header ID as an index into an array in the switch SRAM memory (step 3). After storing a packet header, the programmable switch retrieves the corresponding payload from the RDMA servers (step 4). The programmable switch then combines the payload with the stored header using the ID and outputs the transformed packet (step 5).

## Requirements
To setup the Ribosome system you need to deploy the P4 code on a programmable switch equipped with Intel Tofino ASIC and the RDMA server agent on one or more servers equipped with ConnectX-5 NICs.

For more details, see the dedicated repositories:
* [Ribosome-P4](https://github.com/Ribosome-Packet-Processor/Ribosome-P4)
* [Ribosome-RDMA-Server-Agent](https://github.com/Ribosome-Packet-Processor/Ribosome-RDMA-Server-Agent)

You also need a NF attached to the Ribosome switch.
We do not provide the NFs in this repository, but [here](https://github.com/Ribosome-Packet-Processor/Ribosome-experiments) you can find the ones we used for the evaluation in the Ribosome paper.

## Configuration

To configure the Ribosome switch for your use cases, please see the [Ribosome-P4](https://github.com/Ribosome-Packet-Processor/Ribosome-P4) repository.
