# Observatory Architecture

![Observatory](observatory.jpg)
_Hopefully this project's name is self-explanatory._

The [Cosmos](https://cosmos.network/) ecosystem has many blockchains. Many of them don't have an organization hosting reliable blockchain nodes to serve API requests for public use. This leads organizations to either host their own blockchain nodes (an expensive and time-consuming ordeal), or rely on blockchain nodes hosted by the community. Many of the community hosted blockchain nodes suffer from low uptimes, and high latencies as their operators aren't compensated for their work, and therefore lack the motivation and money to provide a high quality service. Using third party hosting solutions such as [Figment](https://figment.io/) isn't practical since they only provide API access for a few of the blockchains in the Cosmos ecosystem.

In order to evaluate whether you should host your own blockchain nodes, or rely on public infrastructure, you can use _Observatory_. _Observatory_ compares uptimes and latencies of API requests made to any Cosmos URLs you want to monitor.

Blockchain nodes will be evaluated by making an API request to the blockchain node's REST API endpoint `/cosmos/base/tendermint/v1beta1/blocks/latest` once a minute. For example, https://rest.cosmos.directory/osmosis/cosmos/base/tendermint/v1beta1/blocks/latest. This API endpoint was chosen as it queries commonly used blockchain data (blocks and transactions).

The following data will be recorded in the database for evaluation (refer complete [schema](https://github.com/leapwallet/observatory/blob/staging/prisma/schema.prisma)):

- chainName
- url
- httpResponseCode
- responseTime
- type : which can be one of
  - ecostake : data for proxy urls [Eg: https://rest.cosmos.directory/{chain}]
  - ingester : Simple load-balancing and retry logic is used to select a node from a given list of nodes. Also, the node list is updated every one hour using cosmos directory [https://status.cosmos.directory/]. Complete logic can be found [here](https://github.com/leapwallet/observatory/tree/staging/src/blockchain-node-urls)

The following [Prometheus](https://prometheus.io/) metrics will also be generated:

- API request durations will be measured using a histogram. A label will be used to distinguish between different URLs. The following buckets will be used: 0.1 s, 0.2 s, 0.3 s, 0.4 s, 0.5 s, 0.6 s, 0.7 s, 0.8 s, 0.9 s, 1 s, 1.1 s, 1.2 s, 1.3 s, 1.4 s, 1.5 s, 1.6 s, 1.7 s, 1.8 s, 1.9 s, 2 s, infinity. For example, the 1.8 s bucket shows API requests that took at most 1.8 s.
- Successful API requests will be measured using a counter. A label will be used to distinguish between different URLs.
- Failed API requests will be measured using a counter. A label will be used to distinguish between different URLs. The successful and failed API request counters can be used to calculate the uptime of a particular blockchain node.

Here's _Observatory_'s [implementation](https://github.com/leapwallet/observatory) (hosted using AWS Fargate), and its [observability](observability.md) docs.

## Contributing

### Installation

1. Install [Node.js 16](https://nodejs.org/en/download/).
2. Clone the repo using one of the following methods:

   - SSH:

     ```shell
     git@github.com:leapwallet/observatory-architecture.git
     ```

   - HTTPS:

     ```shell
     https://github.com/leapwallet/observatory-architecture.git
     ```

3. Change the directory:

   ```shell
   cd observatory-architecture
   ```

4. Install the package manager:

   ```shell
   corepack enable
   ```

5. Install the dependencies:

   ```shell
   yarn
   ```

### Usage

Lint:

```shell
yarn lint:fix
```
