---
title: How to Set Up VPN between Cisco ASR and Palo Alto Networks Firewall.
description: Learn how to build site-to-site IPSEC VPN between PAN NGFW and Cloud VPN.
author: ashishverm
tags: Compute Engine, Cloud VPN, PAN Firewalls
date_published: 2018-01-26
---

This guide walks you through the process to configure the PAN NGFW for
integration with the [Google Cloud VPN Services][cloud_vpn]. This information is
provided as an example only. Please note that this guide is not meant to be a
comprehensive overview of IPsec and assumes basic familiarity with the IPsec
protocol.

[cloud_vpn]: https://cloud.google.com/compute/docs/vpn/overview

## Environment overview

The equipment used in the creation of this guide is as follows:

* Vendor: Palo Alto Networks
* Model: VM-Series
* Software Release: 8.1.0

Although this guide is created with VM-Series exactly the same configuration
also apply to all other PAN NGFW platforms.

## Topology

The topology outlined by this guide is a basic site-to-site IPsec VPN tunnel
configuration using the referenced device:

![Topology](gcp-pan-topology.jpg)

## Before you begin

### Overview

The configuration samples which follow will include numerous value substitutions 
provided for the purposes of example only.  Any references to IP addresses, device IDs,
shared secrets or keys, account information or project names should be replaced with 
the appropriate values for your environment when following this guide.  Values unique 
to your environment will be highlighted in bold.  
This guide is not meant to be a comprehensive setup overview for the device referenced,
but rather is only intended to assist in the creation of IPsec connectivity to Google 
Compute Engine. The following is a high level overview of the configuration process which
will be covered:

* Selecting the appropriate IPsec configuration
* Configuring the internet facing interface of your device (outside interface)
* Configuring Internet Key Exchange (IKE) and IPsec
* Testing the tunnel

### Getting Started

The first step in configuring your Palo Alto Networks VM-series for use with the Google cloud
VPN service is to ensure that the following prerequisite conditions have been met:  

* Palo Alto Networks VM-series online and functional with no faults detected
* Root access to the Palo Alto Networks VM-series
* At least one configured and verified functional internal interface
* One configured and verified functional external interface

### IPsec parameters

For the VM-series IKE configuration, the following details will be used:

|Parameter | Value|
--------- |  -----
|IPsec Mode | `Tunnel mode` |
|Auth protocol | `Pre-shared-key` |
|Key Exchange | `IKEv2` |
|Start | `Auto` |
|Diffie-Hellman (DH) | `Group 14` |
|Authentication | `sha256` |
|Encryption | `aes-256-cbc` |

The IPsec configuration used in this guide is specified below:

| Cipher Role | Cipher |
| ------------| -------|
| Encryption | `aes-256-cbc` |
| Authentication | `sha256` |
| Diffie-Hellman (DH) | `group 14` |
| Lifetime | `10 hours` |

## Configuration – GCP

### IPsec VPN using dynamic routing

For dynamic routing you use [Cloud Router](https://cloud.google.com/router/docs/concepts/overview)
to establish BGP sessions between the 2 peers.
