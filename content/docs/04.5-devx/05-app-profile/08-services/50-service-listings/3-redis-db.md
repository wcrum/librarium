---
title: "Redis"
metaTitle: "Palette Dev Engine Redis Database Service"
metaDescription: "Palette Dev Engine Redis Database Service"
hideToC: false
type: "appTier"
category: ['databases']
hiddenFromNav: false
fullWidth: false
logoUrl: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSjxG5Qb38rX39m1M2p1W4t8H70OKpRY2breg&usqp=CAU"
---

import Tabs from 'shared/components/ui/Tabs';
import WarningBox from 'shared/components/WarningBox';
import InfoBox from 'shared/components/InfoBox';


# Redis

[Redis](https://redis.io/docs/about/) is an open-source (BSD licensed), in-memory data structure store used as a data cache store or database service. Redis has built-in replication, Lua scripting, least recently used eviction, transactions, and different levels of on-disk persistence capabilities. In addition, Redis provides high availability via Redis Sentinel and automatic partitioning with Redis Cluster.

# Add Redis to an App Profile

Use the following steps to add Redis to an app profile.

<br />

## Prerequisite

A Spectro Cloud [account](https://www.spectrocloud.com/get-started/).

<br />

## Enablement

1. Log in to [Palette](console.spectrocloud.com)


2. On the right side of the window, click on the **User Menu** and select **Switch to App Mode**.


3. Navigate to the left **Main Menu** and click on **App Profiles** to create a [new App Profile](/devx/app-profile/create-app-profile/). Provide the following basic information and click **Next**.

|         Parameter           | Description  |
|-----------------------------|-----------------|
|Application Profile Name | A custom name for the app profile.|
|Version (optional)       | The default value is 1.0.0. You can create multiple versions of an app profile using the format **`major.minor.patch`**.
|Description (optional)   | Description of the app profile. | 
|Tag (optional)           |  Assign tags to the app profile.|
 

4. Select the **Redis DB** service and start the configuration.
  

5. Provide the following information to the wizard:
  * **Name:** The database name.
  * **Password:** The password for the database service.
  * **Database Volume Size (GiB):** Select the volume as per the storage volume available in the cluster group and virtual clusters. 

6. Save your changes.
## Validation

* To verify your database service is in the app profile, navigate to the **App Profiles** page, where all your app profiles are listed. Select the app profile to review the service layers. The following screen displays the different service layers that make up the app profile. Ensure Redis is an available service layer.


* Validate the services from the Apps page after app deployment. First, navigate to the **Apps** page, where all your apps are listed. Then, select the app to display the service layers. The color code in the app profile box shows the status of the service deployment.

|**Color Code**| **Description**|
|--------------|--------------|
|Green| Successfully Deployed|
|Blue | Under Deployment|
|Red  | Error State|


# Output Variables

The exposed output variables. Use these variables when connecting higher-level services with the database:



| Parameter              | Output Variable                                                                     | Description                                     |
|------------------------|-------------------------------------------------------------------------------------|-------------------------------------------------|
| Database Username      | `{{.spectro.app.$appDeploymentName.<service-name>.USERNAME}}`              | The database user name.                         |
| Database User Password | `{{.spectro.app.$appDeploymentName.<service-name>.PASSWORD}}`              | The password of the database user name. |
| Service Hostname       | `{{.spectro.app.$appDeploymentName.<service-name>.REDISMSTR_SVC}}`      | The Kubernetes service hostname for the database.                |
| Service Port           | `{{.spectro.app.$appDeploymentName.<service-name>.REDISMSTR_SVC_PORT}}` | The exposed port for the database service.              |
| Namespace           | `{{.spectro.app.$appDeploymentName.<service-name>.REDISMSTR_NS}}` | The Kubernetes namespace the Redis database is deployed to.              |


# Database Password

You can get the database secret by reading the content of the Kubernetes secret created for the database user. To retrieve the password for the Redis database, use the following command format. 

```
kubectl get secret <app-name>-<service-name>-redis-auth \
 -n <app-name>-<service-name>-ns -o jsonpath='{.data.password}' | base64 --decode
```

Replace the values with the respective names.

  * app-name: represents the name of the app provided during the app creation process.
  * service-name: The name of the service layer in the app profile.

Example: 

- App Name: `app-tarfful`

- Service Name: `redis-4`


```
kubectl get secret  app-tarfful-redis-4-redis-auth \
 -n app-tarfful-redis-4-ns -o jsonpath='{.data.password}' | base64 --decode
 .Hr1}%DrA2MFf
```



