# BOSH release for IPSEC encryption

This BOSH release allows easy deployment of a group of VMs,
communicating securely with each other via an IPSEC mesh.

## Components

This leverages the ipsec-tools project, specifically:

* the ```setkey``` utility to set up kernel IPSEC policies.
* the ```racoon``` IKE daemon to negotiate IPSEC connections with other participants in the mesh.

## Use cases

This release can be used in combination with any other BOSH release to secure traffic in flight 
between BOSH deployed VMs.

It can also be used to interoperate with other non-BOSH IPSEC participants, as long as they speak IKEv1
and IPSEC transport mode.

The mesh is opt-in, and does not affect traffic to machines not designated as part of the mesh.

## Usage

Required: A list of IPs to participate in the IPSEC mesh

```
properties:

  ipsec:
    nodes:
      - 10.244.0.34
      - 10.244.0.6
      - 10.244.0.42
      - 10.244.0.30
      - 10.244.0.130
      - 10.244.0.134
      - 10.244.0.138
      - 10.244.0.142
      - 10.244.0.26
      - 10.244.0.14
      - 10.244.0.10
      - 10.244.0.22
```

Further reading: ```jobs/ipsec/spec```


## IPSEC & MTU

IPSEC encapsulation has some IP packet overhead, which means the effective MTU on the network is less
than set on the interface. In some situations, this can cause large packets to be 
silently dropped on misconfigured networks.

This is the case on the Warden containers on Cloud Foundry Runners.

To fix this on Cloud Foundry v192 or newer, set:

```
properties:
  dea_next:
    mtu: 1400
```

MSS-clamping can also be used to partially resolve this issue, if the real cause of the problem is not under your control.

see ```jobs/ipsec/spec```

also see https://github.com/cloudfoundry/warden/pull/79



## BOSH-Lite

Newer 'packer' derived versions have network oddities that prevent IPSEC transport mode by default.

Follow the workaround in https://github.com/cloudfoundry/bosh-lite/issues/193

## CloudFoundry

Tested on CF-194 with provided example manifest.

## Manifests

see ```sample_manifests/```
