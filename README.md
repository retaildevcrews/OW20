# OneWeek 2020: Crew 512 + Crew Cosmonauts

> The goal of this project is to collaborate as a team and be able to integrate and demonstrate some of the different capabilities that Kubernetes + Istio Service Mesh offer.

## Team 1: Traffic Patterns using ISTIO and different version of Helium

- Blue Green Pattern
- Canary Pattern
- Circuit Breaker (Disaster Recovery/HA modes active-active or active standby)
- Use Kiali to show and test traffic flow for all three patterns

### Team 1 Members

- [Alfredo](https://github.com/alfredoihernandez)
- [Jim](https://github.com/jkeane)
- [Kevin](https://github.com/kev-ms)
- [Joseph](https://github.com/jofultz)

### Traffic Patterns Findings

[Documentation](./traffic-patterns/README.md)

## Team 2: Scaling out the application under heavy load

- Use a horizontal pod autoscaler (HPA) resource to scale pods in same cluster
- Auto-scale current cluster by adding new nodes
- *Bonus: Scaling Cosmos DB when the load is high*

### Team 2 Members

- [Kushal](https://github.com/kforeverisback)
- [Gerardo](https://github.com/gortegaMS)
- [Siva](https://github.com/sivamu)
- [Joaquin](https://github.com/joaquinrz)

### Scaling Findings

[Documentation](./autoscaling/README.md)

## Team 3: Multi-Cluster Service Mesh

- Burst/Route Helium traffic to different clusters
- Setup Dashboard showing traffic flow between cluster

### Team 3 Members

- [AK](https://github.com/AAkindele)
- [Anne](https://github.com/aflinchb)
- [Nsikan](https://github.com/PurpleBriar)

### Multi-Cluster Service Mesh Findings

[Documentation](./multicluster/README.md)

![License](https://img.shields.io/badge/license-MIT-green.svg)

## Contributing

This project welcomes contributions and suggestions. Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit [Microsoft Contributor License Agreement](https://cla.opensource.microsoft.com).

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).

For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments
