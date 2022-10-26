---
title: "Building my Raspberry Pi Kubernetes Cluster"
date: 2021-02-23T17:11:12-04:00
draft: false
---
Over 2020 I have been learning everything I can about Kubernetes and container technologies and it has been a very fun and impactful experience. I started out using [Docker-Desktop](https://www.docker.com/products/docker-desktop) and [minikube](https://minikube.sigs.k8s.io/docs/) to get the basics of Docker and Kubernetes down.

Eventually I got to a point where I was feeling pretty comfortable and I really wanted a way to scale up my knowledge and put it to the test. I considered running Kubernetes nodes as VMs inside of a Proxmox host as well as running smaller nodes in the cloud but with my current setup Proxmox felt like a waste of power and running it in the cloud would have been exorbitantly expensive (shocker!).

After some looking around at options I came across blogs by [Alex Ellis](https://www.alexellis.io) and [Jeff Geerling](https://www.jeffgeerling.com), Jeff and Alex both had detailed their journeys towards running Kubernetes clusters with Raspberry Pis running the lightweight Kubernetes distribution, [K3s by Rancher](https://k3s.io)
                            
I had used Raspberry Pis in the past, in fact some of my earliest memories of really getting into computer science were messing around with the Raspberry Pi 1 from 2013. Seeing that I could realistically run a fully featured Kubernetes cluster with Raspberry Pis got me really excited so I began to pick parts out.
                            
![raspi-node](img/k8s-post/node.jpg "Raspberry Pi Node")
                            
Above is a single node of my cluster, I decided to go with a 6-node cluster composed of Raspberry Pi 4 models with 4GB of RAM and 4 64-bit ARM CPU cores at 1.5GHz per node. My cluster has a total of 24GB of RAM and a total of 24 CPU cores clocked at 1.5GHz. It is truly amazing how far Raspberry Pis have come along over the years!

In order to keep all the Pis powered and neat I ordered a Cloudlet Cluster Case from c4labs and a 60 Watt Anker USB power supply. As far as networking goes, I made use of a Gigabit Netgear switch and some flat Cat.6 ethernet cables I had lying around.

![get nodes](img/k8s-post/getnodes.png "Output of a `kubectl get nodes` command")
                            
Once the cluster was all assembled, it was time to install k3s, this stage of the project took a lot of trial and error. I began by using Ubuntu Server 20.04 as my base OS and I did like it, but I felt it was too expensive resource wise and personally I'm just not a huge fan of Ubuntu as a server distro, I feel like there is too much built into it. I eventually tried out the super lightweight [Alpine Linux](https://www.alpinelinux.org/) distribution on the Pis and absolutely loved its features and its resource footprint. I originally installed the OS to MicroSD cards as originally intended for the Pi but I later opted for booting the OS from much speedier 16GB Samsung flashdrives.
                          
![htop](img/k8s-post/htop.png "Output of htop on one of the nodes")

With performance monitoring agents for Datadog and Grafana as well as k3s and an embedded etcd key-value store running left my three primary controller nodes with about 2.5GB RAM free for use on average.

![grafana](img/k8s-post/grafana.png "My grafana dashboard")

As I touched on before, I deployed Grafana to my cluster for performance monitoring and alerts. It was fairly easy to deploy and is extremely flexible. In the near future I would really like to figure out a centrallized logging solution for my network. I really liked the look of an Elastic-Logstash-Kibana stack but unfortunately it is not at this time ARM compatible. I will likely wind up using Fluentd or Grafana Loki for my log collection.

![telegram-alert](img/k8s-post/alert.png "Telegram k8s alert")

Using Grafana I was able to link to a Telegram bot quite easily to send custom alerts to my phone based on resource usage thresholds.

Finally I needed to figure out some storage for the programs I would be running on my cluster. I already owned a 4TB Synology DS218+ NAS so I decided to use the built in NFS storage class in Kubernetes to make my Persistent Volumes on the Synology.
                            
![synology](img/k8s-post/synology.jpg "My Synology")

I have learned a ton throughout this project and still am learning lots. I will likely make some more posts detailing some of the services i'll be running on my cluster and the evolutions it takes.
