# Marbles Blockchain Sample

>Hyperledger Fabric sample Marbles on IBM Blockchain Platform for Multicloud

This code pattern demonstrates setting up a network on the IBM Blockchain Platform and deploying the Marbles smart contract on the network.  Next, we setup our application to interact with the network including identities to submit transactions on the smart contract.  The application is setup with a Node.js server using the Fabric Node SDK to process requests to the network, along with a web interface.

 When the reader has completed this code pattern, they will understand how to:

* Setup a Hyperledger Fabric network on IBM Blockchain Platform
* Install and instantiate smart contract through the IBM Blockchain Platform
* Develop a Node.js server with the Hyperledger Fabric SDK to interact with the deployed network


## About Marbles
- The underlying network for this application is the [Hyperledger Fabric](https://github.com/hyperledger/fabric/tree/master/docs), a Linux Foundation project.  You may want to review these instructions to understand a bit about the Hyperledger Fabric.
- **This demo is to aid a developer learn the basics of chaincode and app development with a Fabric network.**
- This is a `very simple` asset transfer demonstration. Multiple users can create and transfer marbles with each other.

	![](./doc_images/marbles-peek.gif)

# Architecture flow

![](./doc_images/comm_flow.png)

1. The admin will interact with Marbles, our Node.js application, in their browser.
2. This client side JS code will open a websocket to the backend Node.js application. The client JS will send messages to the backend when the admin interacts with the site.
3. Reading or writing the ledger is known as a proposal. This proposal is built by Marbles (via the SDK) and then sent to a blockchain peer.
4. The peer will communicate to its Marbles chaincode container. The chaincode will run/simulate the transaction. If there are no issues it will endorse the transaction and send it back to our Marbles application.
5. Marbles (via the SDK) will then send the endorsed proposal to the ordering service.  The orderer will package many proposals from the whole network into a block.  Then it will broadcast the new block to peers in the network.
6. Finally the peer will validate the block and write it to its ledger. The transaction has now taken effect and any subsequent reads will reflect this change.


# Included components
*	[IBM Blockchain Platform for Multicloud](https://cloud.ibm.com/docs/services/blockchain?topic=blockchain-get-started-console-icp) gives you total control of your blockchain network with a user interface that can simplify and accelerate your journey to deploy and manage blockchain components on IBM Cloud Private.
*	[IBM Cloud Private](https://www.ibm.com/cloud/private) creates a cluster of compute hosts and deploys highly available containers. A Kubernetes cluster lets you securely manage the resources that you need to quickly deploy, update, and scale applications.
* [IBM Blockchain Platform Extension for VS Code](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) is designed to assist users in developing, testing, and deploying smart contracts -- including connecting to Hyperledger Fabric environments.

## Featured technologies
+ [Hyperledger Fabric v1.4](https://hyperledger-fabric.readthedocs.io) is a platform for distributed ledger solutions, underpinned by a modular architecture that delivers high degrees of confidentiality, resiliency, flexibility, and scalability.
+ [Node.js](https://nodejs.org) is an open source, cross-platform JavaScript run-time environment that executes server-side JavaScript code.


### Prerequisites

- [IBM Cloud Private](https://cloud.ibm.com/docs/services/blockchain?topic=blockchain-icp-console-setup)
- [Node v8.x or greater and npm v5.x or greater](https://nodejs.org/en/download/)
- [VSCode version 1.26 or greater](https://code.visualstudio.com)
- [IBM Blockchain Platform Extension for VSCode](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform)

## Running the application

Follow these steps to set up and run this code pattern. The steps are described in detail below.

### Steps

1. [Clone the repo](#1-clone-the-repo)
2. [Package the smart contract](#2-package-the-smart-contract)
3. [Install IBM Blockchain Platform for Multicloud](#3-install-ibm-blockchain-platform-for-multicloud)
4. [Build a network](#4-build-a-network)
5. [Deploy the smart contract on the network](#5-deploy-the-smart-contract-on-the-network)
6. [Connect application to the network](#6-connect-application-to-the-network)
7. [Run the application](#7-run-the-application)


## 1. Clone the repo

Clone this repository in a folder your choice:

```bash
git clone --branch v5.0 https://github.com/IBM-Blockchain/marbles.git
cd marbles
```

Setup your Golang environment:

```bash
mkdir ~/go
export GOPATH=~/go
cd ~/go
go get github.com/hyperledger/fabric/core/chaincode/shim
mkdir ~/go/src/marbles
cp -r <directory>/marbles/chaincode/src/marbles/ ./src/marbles/
```

## 2. Package the smart contract

We will use the IBM Blockchain Platform extension to package the smart contract.

* Open Visual Studio code and open the `contract` folder from this repository that was cloned earlier.

> Marbles is a [Golang](https://golang.org) smart contract. To properly package the smart contract you must have the environment variable `GOPATH` set. For example:
> ```
> GOPATH=~/go code ~/go/src/marbles
> ```


* Press the `F1` key to see the different VS code options. Choose `IBM Blockchain Platform: Package a Smart Contract Project`.

* Click the `IBM Blockchain Platform` extension button on the left. This will show the packaged contracts on top and the blockchain connections on the bottom.

* Next, right click on the packaged contract to export it and choose `Export Package`.

* Choose a location on your machine and save `.cds` file.  We will use this packages smart contract later to deploy on our work.

<p align="center">
  <img src="./doc-gifs/package-smart-contract.gif">
</p>

Now, we will start creating our Hyperledger Fabric network.

## 3. Install IBM Blockchain Platform for Multicloud

* Once you have obtaned the IBM Blockchain Platform for Multicloud software, you need to load the archive into IBM Cloud Private.  You will need access to the IBM Private Cloud command line interface tools for this step.

1. Login to the ICP server and the Private Docker Registry
	
```bash
/usr/local/bin/cloudctl login -u $ICP_USER -p $ICP_PASS
docker login -u $ICP_USER -p $ICP_PASS mycluster.icp:8500 
```

2. Load the archive

`cloudctl catalog load-ppa-archive --archive ~/ibm-blockchain-platform-prod-2.0.0.tar.gz`

3. (Optional) Create Persistent Volumes

In this demonstration we will use Kubernetes [Local Storage](https://kubernetes.io/docs/concepts/storage/volumes/#local)



<br>
<p align="center">
  <img src="docs/doc-gifs/create-ibm-kubernetes-service.gif">
</p>
<br>

* Create the [IBM Blockchain Platform](https://console.bluemix.net/catalog/services/blockchain/) service on the IBM Cloud.  You can find the service in the `Catalog`, and give a name.

<br>
<p align="center">
  <img src="docs/doc-gifs/create-ibm-blockchain-2-service.gif">
</p>
<br>

* After your kubernetes cluster is up and running, you can deploy your IBM Blockchain Platform on the cluster.  The service walks through few steps and finds your cluster on the IBM Cloud to deploy the service on.

<br>
<p align="center">
  <img src="docs/doc-gifs/deployblockchainservice.gif">
</p>
<br>

* Once the Blockchain Platform is deployed on the Kubernetes cluster, you can launch the console to start operating on your blockchain network.

<br>
<p align="center">
  <img src="docs/doc-gifs/launch-ibm-blockchain.gif">
</p>
<br>

## 4. Build a network

We will build a network as provided by the IBM Blockchain Platform [documentation](https://console.bluemix.net/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).  This will include creating a channel with a single peer organization with its own MSP and CA (Certificate Authority), and an orderer organization with its own MSP and CA. We will create the respective identities to deploy peers and operate nodes.

### Create your organization and your entry point to your blockchain

* #### Create your peer organization CA
  - Click <b>Add Certificate Authority</b>.
  - Click <b>IBM Cloud</b> under <b>Create Certificate Authority</b> and <b>Next</b>.
  - Give it a <b>Display name</b> of `Org1 CA`.  
  - Specify an <b>Admin ID</b> of `admin` and <b>Admin Secret</b> of `adminpw`.

<br>
<p align="center">
  <img src="docs/doc-gifs/createpeerorg1-CA.gif">
</p>
<br>


* #### Use your CA to register identities
  - Select the <b>Org 1 CA</b> Certificate Authority that we created.
  - First, we will register an admin for our organization "org1". Click on the <b>Register User</b> button.  Give an <b>Enroll ID</b> of `org1admin`, and <b>Enroll Secret</b> of `org1adminpw`.  Click <b>Next</b>.  Set the <b>Type</b> for this identity as `client` and select from any of the affiliated organizations from the drop-down list. We will leave the <b>Maximum enrollments</b> and <b>Add Attributes</b> fields blank.
  - We will repeat the process to create an identity of the peer. Click on the <b>Register User</b> button.  Give an <b>Enroll ID</b> of `peer1`, and <b>Enroll Secret</b> of `peer1pw`.  Click <b>Next</b>.  Set the <b>Type</b> for this identity as `peer` and select from any of the affiliated organizations from the drop-down list. We will leave the <b>Maximum enrollments</b> and <b>Add Attributes</b> fields blank.

<br>
<p align="center">
  <img src="docs/doc-gifs/org1caregister-identities.gif">
</p>
<br>


* #### Create the peer organization MSP definition
  - Navigate to the <b>Organizations</b> tab in the left navigation and click <b>Create MSP definition</b>.
  - Enter the <b>MSP Display name</b> as `Org1 MSP` and an <b>MSP ID</b> of `org1msp`.
  - Under <b>Root Certificate Authority</b> details, specify the peer CA that we created `Org1 CA` as the root CA for the organization.
  - Give the <b>Enroll ID</b> and <b>Enroll secret</b> for your organization admin, `org1admin` and `org1adminpw`. Then, give the Identity name, `Org1 Admin`.
  - Click the <b>Generate</b> button to enroll this identity as the admin of your organization and export the identity to the wallet. Click <b>Export</b> to export the admin certificates to your file system. Finally click <b>Create MSP definition</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/peerorgmsp-def.gif">
</p>
<br>


* Create a peer
  - On the <b>Nodes</b> page, click <b>Add peer</b>.
  - Click <b>IBM Cloud</b> under Create a new peer and <b>Next</b>.
  - Give your peer a <b>Display name</b> of `Peer Org1`.
  - On the next screen, select `Org1 CA` as your <b>Certificate Authority</b>. Then, give the <b>Enroll ID</b> and <b>Enroll secret</b> for the peer identity that you created for your peer, `peer1`, and `peer1pw`. Then, select the <b>Administrator Certificate (from MSP)</b>, `Org1 MSP`, from the drop-down list and click <b>Next</b>.
  - Give the <b>TLS Enroll ID</b>, `admin`, and <b>TLS Enroll secret</b>, `adminpw`, the same values are the Enroll ID and Enroll secret that you gave when creating the CA.  Leave the <b>TLS CSR hostname</b> blank.
  - The last side panel will ask you to <b>Associate an identity</b> and make it the admin of your peer. Select your peer admin identity `Org1 Admin`.
  - Review the summary and click <b>Submit</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/createpeer.gif">
</p>
<br>

### Create the node that orders transactions

* #### Create your orderer organization CA
  - Click <b>Add Certificate Authority</b>.
  - Click <b>IBM Cloud</b> under <b>Create Certificate Authority</b> and <b>Next</b>.
  - Give it a unique <b>Display name</b> of `Orderer CA`.  
  - Specify an <b>Admin ID</b> of `admin` and <b>Admin Secret</b> of `adminpw`.

<br>
<p align="center">
  <img src="docs/doc-gifs/ordererorg-ca.gif">
</p>
<br>

* #### Use your CA to register orderer and orderer admin identities
  - In the <b>Nodes</b> tab, select the <b>Orderer CA</b> Certificate Authority that we created.
  - First, we will register an admin for our organization. Click on the <b>Register User</b> button.  Give an <b>Enroll ID</b> of `ordereradmin`, and <b>Enroll Secret</b> of `ordereradminpw`.  Click <b>Next</b>.  Set the <b>Type</b> for this identity as `client` and select from any of the affiliated organizations from the drop-down list. We will leave the <b>Maximum enrollments</b> and <b>Add Attributes</b> fields blank.
  - We will repeat the process to create an identity of the orderer. Click on the <b>Register User</b> button.  Give an <b>Enroll ID</b> of `orderer1`, and <b>Enroll Secret</b> of `orderer1pw`.  Click <b>Next</b>.  Set the <b>Type</b> for this identity as `peer` and select from any of the affiliated organizations from the drop-down list. We will leave the <b>Maximum enrollments</b> and <b>Add Attributes</b> fields blank.

<br>
<p align="center">
  <img src="docs/doc-gifs/orderercaregister-identities.gif">
</p>
<br>


* #### Create the orderer organization MSP definition
  - Navigate to the <b>Organizations</b> tab in the left navigation and click <b>Create MSP definition</b>.
  - Enter the <b>MSP Display name</b> as `Orderer MSP` and an <b>MSP ID</b> of `orderermsp`.
  - Under <b>Root Certificate Authority</b> details, specify the peer CA that we created `Orderer CA` as the root CA for the organization.
  - Give the <b>Enroll ID</b> and <b>Enroll secret</b> for your organization admin, `ordereradmin` and `ordereradminpw`. Then, give the <b>Identity name</b>, `Orderer Admin`.
  - Click the <b>Generate</b> button to enroll this identity as the admin of your organization and export the identity to the wallet. Click <b>Export</b> to export the admin certificates to your file system. Finally click <b>Create MSP definition</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/ordererorgmsp-def.gif">
</p>
<br>

* #### Create an orderer
  - On the <b>Nodes</b> page, click <b>Add orderering service</b>.
  - Click <b>IBM Cloud</b> and proceed with <b>Next</b>.
  - Give your peer a <b>Display name</b> of `Orderer`.
  - On the next screen, select `Orderer CA` as your <b>Certificate Authority</b>. Then, give the <b>Enroll ID</b> and <b>Enroll secret</b> for the peer identity that you created for your orderer, `orderer1`, and `orderer1pw`. Then, select the <b>Administrator Certificate (from MSP)</b>, `Orderer MSP`, from the drop-down list and click <b>Next</b>.
  - Give the <b>TLS Enroll ID</b>, `admin`, and <b>TLS Enroll secret</b>, `adminpw`, the same values are the Enroll ID and Enroll secret that you gave when creating the CA.  Leave the <b>TLS CSR hostname</b> blank.
  - The last side panel will ask to <b>Associate an identity</b> and make it the admin of your peer. Select your peer admin identity `Orderer Admin`.
  - Review the summary and click <b>Submit</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/createorderer.gif">
</p>
<br>

* #### Add organization as Consortium Member on the orderer to transact
  - Navigate to the <b>Nodes</b> tab, and click on the <b>Orderer</b> that we created.
  - Under <b>Consortium Members</b>, click <b>Add organization</b>.
  - From the drop-down list, select `Org1 MSP`, as this is the MSP that represents the peer's organization org1.
  - Click <b>Submit</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/addorg-orderer.gif">
</p>
<br>


### Create and join channel

* #### Create the channel
  - Navigate to the <b>Channels</b> tab in the left navigation.
  - Click <b>Create channel</b>.
  - Give the channel a name, `mychannel`.
  - Select the orderer you created, `Orderer` from the orderers drop-down list.
  - Select the channel member under the Organization section, This should be `Org1 MSP (org1msp)`.
  - Select the MSP identifying the organization of the channel creator from the drop-down list. This should be `Org1 MSP (org1msp)`.
  - Click <b>Add</b> next to your organization. Make your organization an <b>Operator</b>.
  - Select identity under the Channel creator organization section as `Org1 Admin`.
  - Click <b>Create channel</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/createchannel.gif">
</p>
<br>


* #### Join your peer to the channel
  - Click <b>Join channel</b> to launch the side panels.
  - Select your `Orderer` and click <b>Next</b>.
  - Enter the name of the channel you just created. `mychannel` and click <b>Next</b>.
  - Select which peers you want to join the channel, click `Peer Org1` .
  - Click <b>Join Channel</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/joinchannel.gif">
</p>
<br>


## 5. Deploy the smart contract on the network


* #### Install a smart contract
  - Click the <b>Smart contracts</b> tab to install the smart contract.
  - Click <b>Install smart contract</b> to upload the Fabcar smart contract package file, which you packaged earlier using the Visual Studio code extension.
  - Click on <b>Add file</b> and find your packaged smart contract.  
  - Once the contract is uploaded, click <b>Install</b>.


<br>
<p align="center">
  <img src="docs/doc-gifs/install-smart-contract.gif">
</p>
<br>

* #### Instantiate smart contract
  - On the smart contracts tab, find the smart contract from the list installed on your peers and click <b>Instantiate</b> from the overflow menu on the right side of the row.
  - On the side panel that opens, select the channel, `mychannel` to instantiate the smart contract on. Click <b>Next</b>.
  - Select the organization members to be included in the policy, `org1msp`.  Click <b>Next</b> twice.
  - Give <b>Function name</b> of `initLedger` and leave <b>Arguments</b> blank.
  - Click <b>Instantiate</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/instantiatesmart-contract.gif">
</p>
<br>

## 6. Connect application to the network

* #### Connect with sdk through connection profile
  - Under the Instantiated Smart Contract, click on `Connect with SDK` from the overflow menu on the right side of the row.
  - Choose from the dropdown for <b>MSP for connection</b>, `org1msp`.
  - Choose from <b>Certificate Authority</b> dropdown, `Org1 CA`.
  - Download the connection profile by scrolling down and clicking <b>Download Connection Profile</b>.  This will download the connection json which we will use soon to establish connection.
  - You can click <b>Close</b> once the download completes.

<br>
<p align="center">
  <img src="docs/doc-gifs/connectwith-sdk.gif">
</p>
<br>

* #### Create an application admin
  - Go to the <b>Nodes</b> tab on the left bar, and under <b>Certificate Authorities</b>, choose your organization CA, <b>Org1 CA</b>.
  - Click on <b>Register user</b>.
  - Give an <b>Enroll ID</b> and <b>Enroll Secret</b> to administer your application users, `app-admin` and `app-adminpw`.
  - Choose `client` as <b>Type</b>.
  - You can leave the <b>Maximum enrollments</b> blank.
  - Under <b>Attributes</b>, click on <b>Add attribute</b>.  Give attribute as `hf.Registrar.Roles` = `*`.  This will allow this identity to act as registrar and issues identities for our app.  Click <b>Add-attribute</b>.
  - Click <b>Register</b>.

<br>
<p align="center">
  <img src="docs/doc-gifs/register-app-admin.gif">
</p>
<br>


* #### Update application connection
  - Copy the connection profile you downloaded into [server folder](web-app/server)
  - Update the [config.json](web-app/server/config.json) file with:
    - The connection json file name you downloaded.
    - The <b>enroll id</b> and <b>enroll secret</b> for your app admin, which we earlier provided as `app-admin` and `app-adminpw`.
    - The orgMSP ID, which we provided as `org1msp`.
    - The caName, which can be found in your connection json file under "organization" -> "org1msp" -> certificateAuthorities". This would be like an IP address and a port.
    - The username you would like to register.
    - Update gateway discovery to `{ enabled: true, asLocalhost: false }` to connect to IBP.

> the current default setup is to connect to a local fabric instance from VS Code

```js
{
    "connection_file": "mychannel_fabcar_profile.json",
    "appAdmin": "app-admin",
    "appAdminSecret": "app-adminpw",
    "orgMSPID": "org1msp",
    "caName": "169.46.208.151:30404",
    "userName": "user1",
    "gatewayDiscovery": { "enabled": true, "asLocalhost": false }
}
```


## 7. Run the application

* #### Enroll admin
  - First, navigate to the `web-app` directory, and install the node dependencies.
    ```bash
    cd web-app/server
    npm install
    ```

  - Run the `enrollAdmin.js` script
    ```bash
    node enrollAdmin.js
    ```

  - You should see the following in the terminal:
    ```bash
    msg: Successfully enrolled admin user app-admin and imported it into the wallet
    ```

* #### Register User
  - Run the `registerUser.js` script.
    ```bash
    node registerUser.js
    ```

  - You should see the following in the terminal:
    ```bash
    Successfully registered and enrolled admin user user1 and imported it into the wallet
    ```



* #### Start the application server
  - From the `server` directory, start the server.

    ```bash
    npm start
    ```

* #### Start the web client
  - In a new terminal, open the web client folder and install the dependencies.
    ```bash
    cd web-app/client
    npm install
    ```

  - Start the client:
    ```bash
    npm start
    ```

You can find the app running at http://localhost:4200/

<br>
<p align="center">
  <img src="docs/doc-gifs/application-UI.gif">
</p>
<br>

You can go to the IBM Blockchain Platform console to monitor your users and get information on your channel including the blocks added.

<br>
<p align="center">
  <img src="docs/doc-gifs/channel-blocks.gif">
</p>
<br>


## Troubleshooting
* If you encounter an error ``discover error: access denied``, you need to set the `gatewayDiscovery` properly in your `config.json` file. This is <b>REQUIRED</b>  You must set it as follows to connect to IBP:

                 `"gatewayDiscovery": {"enabled": true, "asLocalhost": false }`

## Links
* [Hyperledger Fabric Docs](http://hyperledger-fabric.readthedocs.io/en/latest/)
* [IBM Code Patterns for Blockchain](https://developer.ibm.com/patterns/category/blockchain/)

## License
This code pattern is licensed under the Apache Software License, Version 2. Separate third-party code objects invoked within this code pattern are licensed by their respective providers pursuant to their own separate licenses. Contributions are subject to the [Developer Certificate of Origin, Version 1.1 (DCO)](https://developercertificate.org/) and the [Apache Software License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Apache Software License (ASL) FAQ](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)
