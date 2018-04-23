# Getting started with RIPE Atlas
[RIPE Atlas](https://atlas.ripe.net/) is a measurement platform consisting of a global network of probes hosted by volunteers. RIPE probes are small USB-powered devices connected to the router of a host. You can find more information about RIPE probes [here](https://atlas.ripe.net/about/probes/). Currently, approximately 10,000 RIPE probes are deployed across 3,613 [autonomous systems](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)). These probes are spread across 179 countries. An upto-date description of the network coverage of RIPE Atlas probes can be found [here](https://atlas.ripe.net/results/maps/network-coverage/).

## Measurement platform and credit system
As a global measurement platform, RIPE Atlas allows users to run network measurements (e.g. traceroutes, pings etc.) from RIPE probes. Using RIPE probes acorss the globe, users can measure characteristics of the Internet connectivity in distant networks (or autonomous systems). To do so, users must spend `mreasurement credits` in return for the measurement task performed by the platform. Note that credits are needed only when a user wishes to perform measurements of their own. Downloading the results of public measurements run by other users of the platform is free.

To begin interacting with the platform, make an account on [atlas.ripe.net](atlas.ripe.net). One can interact with the platform through their [website](https://atlas.ripe.net/) or through APIs provided by the Atlas development team. We will first explore the functionality via the website. Note that you can explore the public measurements run on the platform by various users [here](https://atlas.ripe.net/measurements/) without having to make an account or creating an API key. However, to run your own measurements, you need to login to the Atlas website and create an API key. We will discuss how to create an API key in the following section called `User defined measurements`.

After logging in, you can visit your Atlas dashboard [here](https://atlas.ripe.net/my/) to see details about the measurements you have run in the recent past, credits in your account and so on. Credits on RIPE Atlas can be earned via three mechanisms: (1) by hosting a RIPE probe in your home network, (2) by requesting a donation from the RIPE Atlas team and (3) by getting a credit transfer from a willing friend. Do consider getting credits through any of the three ways if you intend to run measurements of your own.

### Find RIPE probes
Each RIPE probe is uniquely identified by an `ID`. A list of all RIPE probes, their `ID`s and networks they are located in is available [here](https://atlas.ripe.net/probes/). This list allows you to find probes in a given country or network of your choice.

### Measurement Type
Using RIPE Atlas, one can perform a number of Internet measurements, like `ping`, `traceroute`, `DNS` resolutions etc. Each measurement has a certain cost associated with it. This cost the amount of measurement credit you will need to spend to run the measurement. This is explained in detail [here](https://atlas.ripe.net/docs/credits/). In few words, a traceroute to single destination costs 30 credits and a ping costs 10 credits. This means traceroutes are 3X more expensive compared to pings in general.

## User-defined Measurements
In this sub-section we will discuss the process of launching one's measurements using RIPE Atlas. First step is to create an API key via the RIPE Atlas website. While logged in withyour credentials, navigate to [https://atlas.ripe.net/keys/](https://atlas.ripe.net/keys/). Click on the "Create API Key" Button:

![alt text](https://github.com/racheesingh/ripe-atlas-starter/blob/master/api-create-key.png "create api key")

This should lead to a page where you can add the name of your API key and what permissions you want to give to it. The important thing to note here is that you should select "Schedule a new measurement" for this key if you want to use it for scheduling your own measurements. The filled form should look like this:

<img src="https://github.com/racheesingh/ripe-atlas-starter/blob/master/add-api-key.png" width="400" align="middle">

Keep track of the API key for future reference. Now we will work on using this API key for launching measurements from RIPE Atlas. RIPE Atlas provides a Python library called [Costeau](https://github.com/RIPE-NCC/ripe-atlas-cousteau) for scheduling measurements programmatically. Install the API from source on Github and use the following snippet to import the classes relevant for scheduling a measurement:

```python
from datetime import datetime
    from ripe.atlas.cousteau import (
      Ping,
      Traceroute,
      AtlasSource,
      AtlasCreateRequest
    )

ATLAS_API_KEY = <insert your API key>
 ```

Then, we define an IPv4 traceroute measurement towards `www.github.com`. The `description` field is a string of your choice to describe what the measurement is doing.
 
 ```python
traceroute = Traceroute(
     af=4,
     target="www.github.com",
     description="trace to Github",
)
```
Now, we will select a set of source probes to run this traceroute from by asking the `AtlasSource` API to fetch 5 RIPE probes in Iran (specified by `country=IR` argument, `IR` is the ISO country code for Iran).

```python
source = AtlasSource(type="country", value="IR", requested=5)
```

We can put the source and the traceroute together into an `AtlasCreateRequest` which creates a measurement request. the `is_oneoff` field specifies if you want to schedule this measurement to run once as opposed to running it at a regular interval. Note that until now the meausrement has not been fired, no credits have been used. The call to `create` the `atlas_request` object fires the measurement. The first return value from `create` is a flag specifying if the measurement succeeded. 

```python
atlas_request = AtlasCreateRequest(
    start_time=datetime.utcnow(),
    key=ATLAS_API_KEY,
    measurements=[traceroute],
    sources=[source],
    is_oneoff=True
)
(is_success, response) = atlas_request.create()
```
## Using existing measurements
-- API for pulling measurements
-- Same for UDMs
