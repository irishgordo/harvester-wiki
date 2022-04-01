## Prerequisites

A running Harvester Environment.


## APIs
We will use Harvester HCI mode installation as an example, the server endpoint will be something similar to `https://harvester-ip:443`, and this is the base URL of all of the API calls.

### Authentication & Authorization

Harvester defaults to using `rancher` mode authentication. In this mode, the Harvester uses the Rancher server as a middleware for both authentication and authorization. For other supported authentication types, please refer to the docs [here](https://github.com/harvester/harvester/blob/master/docs/authentication.md).

To check the auth-mode of your Harvester server visit the `/v1-public/auth-modes`

```JSON
{
    "modes": [
        "rancher"
    ]
}
```

Users can login using the username and password with: `/v3-public/localProviders/local?action=login`

```JSON
{
    "username":"admin",
    "password":"password",
    "description":"API Session",
    "responseType":"cookie",
    "ttl":57600000
}
```
the response cookies will contains a `R_SESS` key and a token, save this token somewhere and it will be used for future authenticated API calls: e.g,
```
R_SESS: token-hbxw6%3Attcmtwrrk2l7p4sshklpjnmx27gzk5mgz9cxzrtm75qlh47mkx2m7b
```

## /v1 APIs
Go to the `https://harvester-ip:30443/v1`, this is the standard RESTful API of Harvester server, the API spec reference can be found in [here](https://github.com/rancher/api-spec/blob/master/specification.md).
This API will list and watch all the Kubernetes CRDs, and users can perform a regular CRUD operation against those CRD objects.

As a client implementation example, you may refer to the [go-harvester](https://github.com/harvester/go-harvester) which is a Go client implementation for the Harvester API.


### GET /v1
since `/v1` API is secured with the authentication, so u will need to add the `R_SESS: token-xxx` to the request headers.


![Imgur](https://imgur.com/k5dRbrm.png)

### POST /v1
e.g if u want to create a VM using the RESTful API you can make a POST request of the `/v1/kubevirt.io.virtualmachines`, 

and an example of request data will be:

```
{
   "apiVersion": "kubevirt.io/v1",
   "kind": "VirtualMachine",
   "metadata": {
      "name": "test",
      "namespace": "default",
   },
   "spec": {
      "dataVolumeTemplates": [
         {
            "apiVersion": "cdi.kubevirt.io/v1beta1",
            "kind": "DataVolume",
            "metadata": {
               "annotations": {
                  "harvesterhci.io/imageId": "default/image-mxqpm"
               },
               "creationTimestamp": null,
               "name": "gc-harv-disk-0-fzopl"
            },
            "spec": {
               "pvc": {
                  "accessModes": [
                     "ReadWriteMany"
                  ],
                  "resources": {
                     "requests": {
                        "storage": "10Gi"
                     }
                  },
                  "storageClassName": "longhorn-image-mxqpm",
                  "volumeMode": "Block"
               },
               "source": {
                  "blank": {}
               }
            }
         },
         {
            "apiVersion": "cdi.kubevirt.io/v1beta1",
            "kind": "DataVolume",
            "metadata": {
               "creationTimestamp": null,
               "name": "gc-harv-disk-1-bquaq"
            },
            "spec": {
               "pvc": {
                  "accessModes": [
                     "ReadWriteMany"
                  ],
                  "resources": {
                     "requests": {
                        "storage": "200Gi"
                     }
                  },
                  "storageClassName": "longhorn",
                  "volumeMode": "Block"
               },
               "source": {
                  "blank": {}
               }
            }
         }
      ],
      "running": true,
      "template": {
         "metadata": {
            "annotations": {
               "harvesterhci.io/diskNames": "[\"gc-harv-disk-0-fzopl\",\"gc-harv-disk-1-bquaq\"]",
               "harvesterhci.io/sshNames": "[]"
            },
            "creationTimestamp": null,
            "labels": {
               "harvesterhci.io/creator": "harvester",
               "harvesterhci.io/vmName": "gc-harv"
            }
         },
         "spec": {
            "domain": {
               "cpu": {
                  "cores": 8,
                  "sockets": 1,
                  "threads": 1
               },
               "devices": {
                  "disks": [
                     {
                        "bootOrder": 2,
                        "cdrom": {
                           "bus": "sata"
                        },
                        "name": "disk-0"
                     },
                     {
                        "bootOrder": 1,
                        "disk": {
                           "bus": "virtio"
                        },
                        "name": "disk-1"
                     },
                     {
                        "disk": {
                           "bus": "virtio"
                        },
                        "name": "cloudinitdisk"
                     }
                  ],
                  "inputs": [
                     {
                        "bus": "usb",
                        "name": "tablet",
                        "type": "tablet"
                     }
                  ],
                  "interfaces": [
                     {
                        "bridge": {},
                        "macAddress": "ea:d6:55:49:a0:5e",
                        "model": "virtio",
                        "name": "default"
                     }
                  ]
               },
               "machine": {
                  "type": "q35"
               },
               "resources": {
                  "requests": {
                     "memory": "10Gi"
                  }
               }
            },
            "evictionStrategy": "LiveMigrate",
            "hostname": "gc-harv",
            "networks": [
               {
                  "multus": {
                     "networkName": "vlan1"
                  },
                  "name": "default"
               }
            ],
            "volumes": [
               {
                  "dataVolume": {
                     "name": "gc-harv-disk-0-fzopl"
                  },
                  "name": "disk-0"
               },
               {
                  "dataVolume": {
                     "name": "gc-harv-disk-1-bquaq"
                  },
                  "name": "disk-1"
               },
               {
                  "cloudInitNoCloud": {
                     "userData": "ssh_authorized_keys:\n  - 'github:test'\nwrite_files:\n  - path: /etc/conf.d/qemu-guest-agent\n    content: GA_PATH=/dev/vport2p1\n    owner: root\n    permissions: '0644'\n"
                  },
                  "name": "cloudinitdisk"
               }
            ]
         }
      }
   }
}
```
