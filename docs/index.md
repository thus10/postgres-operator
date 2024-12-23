<h1>Concepts</h1>

The Postgres [operator](https://coreos.com/blog/introducing-operators.html)
manages PostgreSQL clusters on Kubernetes (K8s):\
Postgres Operator 管理 Kubernetes (K8s) 上的 PostgreSQL 集群：

1. The operator watches additions, updates, and deletions of PostgreSQL cluster
   manifests and changes the running clusters accordingly.  For example, when a
   user submits a new manifest, the operator fetches that manifest and spawns a
   new Postgres cluster along with all necessary entities such as K8s
   StatefulSets and Postgres roles.  See this
   [Postgres cluster manifest](https://github.com/zalando/postgres-operator/blob/master/manifests/complete-postgres-manifest.yaml)
   for settings that a manifest may contain.\
   1. operator监视PostgreSQL集群清单的添加、更新和删除，并相应地更改正在运行的集群。
   例如，当用户提交新的清单时，操作员会获取该清单并生成一个新的 Postgres 集群以及所有必要的实体（例如 K8s StatefulSets 和 Postgres 角色）。请参阅此 [Postgres集群清单](manifests/complete-postgres-manifest.yaml)了解清单可能包含的设置。

2. The operator also watches updates to [its own configuration](https://github.com/zalando/postgres-operator/blob/master/manifests/configmap.yaml)
   and alters running Postgres clusters if necessary.  For instance, if the
   Docker image in a pod is changed, the operator carries out the rolling
   update, which means it re-spawns pods of each managed StatefulSet one-by-one
   with the new Docker image.\
   2. Operator 还会监视[其自身配置](manifests/configmap.yaml) 的更新，并在必要时更改正在运行的 Postgres 集群。  例如，如果 Pod 中的 Docker 镜像发生更改，Operator 会执行滚动更新，这意味着它会使用新的 Docker 镜像一一重新生成每个托管 StatefulSet 的 Pod。

3. Finally, the operator periodically synchronizes the actual state of each Postgres cluster with the desired state defined in the cluster's manifest.\
   3. 最后，operator定期同步各个节点的实际状态。Postgres 集群具有集群清单中定义的所需状态。

4. The operator aims to be hands free as configuration works only via manifests. This enables easy integration in automated deploy pipelines with no access to K8s directly.\
   4. operator的目标是解放双手，因为配置只能通过清单进行。这使得可以轻松集成到自动化部署管道中，而无需直接访问 K8。


## Scope

The scope of the Postgres Operator is on provisioning, modifying configuration
and cleaning up Postgres clusters that use Patroni, basically to make it easy
and convenient to run Patroni based clusters on K8s. The provisioning
and modifying includes K8s resources on one side but also e.g. database
and role provisioning once the cluster is up and running. We try to leave as
much work as possible to K8s and to Patroni where it fits, especially
the cluster bootstrap and high availability. The operator is however involved
in some overarching orchestration, like rolling updates to improve the user
experience.\
Postgres Operator 的作用范围包括提供、修改配置和清理使用 Patroni 的 Postgres 集群，基本上是为了让在 K8s 上运行基于 Patroni 的集群变得简单方便。提供和修改包括 K8s 资源的一侧，但也包括例如数据库和角色提供，一旦集群启动并运行。我们尽量将尽可能多的工作留给 K8s 和 Patroni，特别是在集群引导和高可用性方面。然而，该操作员也参与一些全局编排，如滚动更新以改善用户体验。

Monitoring or tuning Postgres is not in scope of the operator in the current
state. However, with globally configurable sidecars we provide enough
flexibility to complement it with other tools like [ZMON](https://opensource.zalando.com/zmon/),
[Prometheus](https://prometheus.io/) or more Postgres specific options.\
监控或调整 Postgres 不在当前操作员的范围内。然而，通过全局可配置的sidecars，我们提供了足够的灵活性，可以与其他工具如[ZMON]、[Prometheus]或更多针对 Postgres 的特定选项相补充。


## Overview of involved entities

Here is a diagram, that summarizes what would be created by the operator, when a
new Postgres cluster CRD is submitted:

![postgresql-operator](diagrams/operator.png "K8s resources, created by operator")

This picture is not complete without an overview of what is inside a single
cluster pod, so let's zoom in:

![pod](diagrams/pod.png "Database pod components")

These two diagrams should help you to understand the basics of what kind of
functionality the operator provides.

## Status

This project is currently in active development. It is however already
[used internally by Zalando](https://jobs.zalando.com/tech/blog/postgresql-in-a-time-of-kubernetes/)
in order to run Postgres clusters on K8s in larger numbers for staging
environments and a growing number of production clusters. In this environment
the operator is deployed to multiple K8s clusters, where users deploy
manifests via our CI/CD infrastructure or rely on a slim user interface to
create manifests.\
该项目目前正在积极开发中。然而，它已经[被 Zalando 内部使用]，以便在 K8 上大量运行 Postgres 集群临时环境和越来越多的生产集群。在此环境中，操作员部署到多个 K8s 集群，用户通过我们的 CI/CD 基础设施部署清单或依靠精简的用户界面来创建清单。

Please, report any issues discovered to https://github.com/zalando/postgres-operator/issues.

## Talks

- "Watching after your PostGIS herd" talk by Felix Kunde, FOSS4G 2021: [video](https://www.youtube.com/watch?v=T96FvjSv98A) | [slides](https://docs.google.com/presentation/d/1IICz2RsjNAcosKVGFna7io-65T2zBbGcBHFFtca24cc/edit?usp=sharing)

- "PostgreSQL on K8S at Zalando: Two years in production" talk by Alexander Kukushkin, FOSSDEM 2020: [video](https://fosdem.org/2020/schedule/event/postgresql_postgresql_on_k8s_at_zalando_two_years_in_production/) | [slides](https://fosdem.org/2020/schedule/event/postgresql_postgresql_on_k8s_at_zalando_two_years_in_production/attachments/slides/3883/export/events/attachments/postgresql_postgresql_on_k8s_at_zalando_two_years_in_production/slides/3883/PostgreSQL_on_K8s_at_Zalando_Two_years_in_production.pdf)

- "Postgres as a Service at Zalando" talk by Jan Mußler, DevOpsDays Poznań 2019: [video](https://www.youtube.com/watch?v=FiWS5m72XI8)

- "Building your own PostgreSQL-as-a-Service on Kubernetes" talk by Alexander Kukushkin, KubeCon NA 2018: [video](https://www.youtube.com/watch?v=G8MnpkbhClc) | [slides](https://static.sched.com/hosted_files/kccna18/1d/Building%20your%20own%20PostgreSQL-as-a-Service%20on%20Kubernetes.pdf)

- "PostgreSQL and Kubernetes: DBaaS without a vendor-lock" talk by Oleksii Kliukin, PostgreSQL Sessions 2018: [video](https://www.youtube.com/watch?v=q26U2rQcqMw) | [slides](https://speakerdeck.com/alexeyklyukin/postgresql-and-kubernetes-dbaas-without-a-vendor-lock)

- "PostgreSQL High Availability on Kubernetes with Patroni" talk by Oleksii Kliukin, Atmosphere 2018: [video](https://www.youtube.com/watch?v=cFlwQOPPkeg) | [slides](https://speakerdeck.com/alexeyklyukin/postgresql-high-availability-on-kubernetes-with-patroni)

- "Blue elephant on-demand: Postgres + Kubernetes" talk by Oleksii Kliukin and Jan Mussler, FOSDEM 2018: [video](https://fosdem.org/2018/schedule/event/blue_elephant_on_demand_postgres_kubernetes/) | [slides (pdf)](https://www.postgresql.eu/events/fosdem2018/sessions/session/1735/slides/59/FOSDEM%202018_%20Blue_Elephant_On_Demand.pdf)

- "Kube-Native Postgres" talk by Josh Berkus, KubeCon 2017: [video](https://www.youtube.com/watch?v=Zn1vd7sQ_bc)

## Posts

- Series of blog posts on how to use the Zalando Operator, configure backups and use etcd as DCS by [thedatabaseme](https://thedatabaseme.de/tag/zalando-operator/), Mar. 2022-23.

- "Zalando Postgres Operator in Production: the way of Helm" by Zangir Kapishov on [medium](https://medium.com/@zkapishov/zalando-postgres-operator-in-production-the-way-of-helm-ccfd639ccb2d), Jan. 2023.

- "Chaos testing of a Postgres cluster managed by the Zalando Postgres Operator" by Nikolay Sivko on [coroot](https://coroot.com/blog/chaos-testing-zalando-postgres-operator), Aug. 2022.

- "Getting started with the Zalando Operator for PostgreSQL" by Daniel Westermann on [dbi services blog](https://www.dbi-services.com/blog/getting-started-with-the-zalando-operator-for-postgresql/), Mar. 2021.

- "Our experience with Postgres Operator for Kubernetes by Zalando" by Nikolay Bogdanov on [Palark blog](https://blog.palark.com/our-experience-with-postgres-operator-for-kubernetes-by-zalando/), Feb. 2021.

- "How to set up continuous backups and monitoring" by Pål Kristensen on [GitHub](https://github.com/zalando/postgres-operator/issues/858#issuecomment-608136253), Mar. 2020.

- "Postgres on Kubernetes with the Zalando operator" by Vito Botta on [has_many :code](https://vitobotta.com/2020/02/05/postgres-kubernetes-zalando-operator/), Feb. 2020.

- "Running PostgreSQL in Google Kubernetes Engine" by Kenneth Rørvik on [Repill Linpro blog](https://www.redpill-linpro.com/techblog/2019/09/28/postgres-in-kubernetes.html), Sep. 2019.

- "Zalando Postgres Operator: One Year Later" by Sergey Dudoladov on [Open Source Zalando](https://opensource.zalando.com/blog/2018/11/postgres-operator/), Nov. 2018
