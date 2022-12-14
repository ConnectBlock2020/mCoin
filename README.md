# mCoin Source Code 

Blockchain
Building the source
For prerequisites and detailed build instructions please read the Installation Instructions.

Building geth requires both a Go (version 1.16 or later) and a C compiler. You can install them using your favourite package manager. Once the dependencies are installed, run

Running geth
Going through all the possible command line flags is out of scope here (please consult our CLI Wiki page), but we've enumerated a few common parameter combos to get you up to speed quickly on how you can run your own geth instance.

Hardware Requirements
Minimum:

CPU with 2+ cores
4GB RAM
1TB free storage space to sync the Mainnet
8 MBit/sec download Internet service
Recommended:

Fast CPU with 4+ cores
16GB+ RAM
High-performance SSD with at least 1TB of free space
25+ MBit/sec download Internet service
Full node on the main Ethereum network
By far the most common scenario is people wanting to simply interact with the Ethereum network: create accounts; transfer funds; deploy and interact with contracts. For this particular use case, the user doesn't care about years-old historical data, so we can sync quickly to the current state of the network. To do so:

$ geth console
This command will:

Start geth in snap sync mode (default, can be changed with the --syncmode flag), causing it to download more data in exchange for avoiding processing the entire history of the Ethereum network, which is very CPU intensive.
Start the built-in interactive JavaScript console, (via the trailing console subcommand) through which you can interact using web3 methods (note: the web3 version bundled within geth is very old, and not up to date with official docs), as well as geth's own management APIs. This tool is optional and if you leave it out you can always attach it to an already running geth instance with geth attach.
A Full node on the Görli test network
Transitioning towards developers, if you'd like to play around with creating Ethereum contracts, you almost certainly would like to do that without any real money involved until you get the hang of the entire system. In other words, instead of attaching to the main network, you want to join the test network with your node, which is fully equivalent to the main network, but with play-Ether only.

$ geth --goerli console
The console subcommand has the same meaning as above and is equally useful on the testnet too.

Specifying the --goerli flag, however, will reconfigure your geth instance a bit:

Instead of connecting to the main Ethereum network, the client will connect to the Görli test network, which uses different P2P bootnodes, different network IDs and genesis states.
Instead of using the default data directory (~/.ethereum on Linux for example), geth will nest itself one level deeper into a goerli subfolder (~/.ethereum/goerli on Linux). Note, on OSX and Linux this also means that attaching to a running testnet node requires the use of a custom endpoint since geth attach will try to attach to a production node endpoint by default, e.g., geth attach <datadir>/goerli/geth.ipc. Windows users are not affected by this.
Note: Although some internal protective measures prevent transactions from crossing over between the main network and test network, you should always use separate accounts for play and real money. Unless you manually move accounts, geth will by default correctly separate the two networks and will not make any accounts available between them.

Full node on the Rinkeby test network
Go Ethereum also supports connecting to the older proof-of-authority based test network called Rinkeby which is operated by members of the community.

$ geth --rinkeby console
Full node on the Ropsten test network
In addition to Görli and Rinkeby, Geth also supports the ancient Ropsten testnet. The Ropsten test network is based on the Ethash proof-of-work consensus algorithm. As such, it has certain extra overhead and is more susceptible to reorganization attacks due to the network's low difficulty/security.

$ geth --ropsten console
Note: Older Geth configurations store the Ropsten database in the testnet subdirectory.

Configuration
As an alternative to passing the numerous flags to the geth binary, you can also pass a configuration file via:

$ geth --config /path/to/your_config.toml
To get an idea of how the file should look like you can use the dumpconfig subcommand to export your existing configuration:

$ geth --your-favourite-flags dumpconfig
Note: This works only with geth v1.6.0 and above.

Docker quick start
One of the quickest ways to get Ethereum up and running on your machine is by using Docker:

docker run -d --name ethereum-node -v /Users/alice/ethereum:/root \
           -p 8545:8545 -p 30303:30303 \
           ethereum/client-go
This will start geth in snap-sync mode with a DB memory allowance of 1GB, as the above command does. It will also create a persistent volume in your home directory for saving your blockchain as well as map the default ports. There is also an alpine tag available for a slim version of the image.

Do not forget --http.addr 0.0.0.0, if you want to access RPC from other containers and/or hosts. By default, geth binds to the local interface and RPC endpoints are not accessible from the outside.

Programmatically interfacing geth nodes
As a developer, sooner rather than later you'll want to start interacting with geth and the Ethereum network via your own programs and not manually through the console. To aid this, geth has built-in support for a JSON-RPC based APIs (standard APIs and geth specific APIs). These can be exposed via HTTP, WebSockets and IPC (UNIX sockets on UNIX based platforms, and named pipes on Windows).

The IPC interface is enabled by default and exposes all the APIs supported by geth, whereas the HTTP and WS interfaces need to manually be enabled and only expose a subset of APIs due to security reasons. These can be turned on/off and configured as you'd expect.

HTTP based JSON-RPC API options:

--http Enable the HTTP-RPC server
--http.addr HTTP-RPC server listening interface (default: localhost)
--http.port HTTP-RPC server listening port (default: 8545)
--http.api API's offered over the HTTP-RPC interface (default: eth,net,web3)
--http.corsdomain Comma separated list of domains from which to accept cross origin requests (browser enforced)
--ws Enable the WS-RPC server
--ws.addr WS-RPC server listening interface (default: localhost)
--ws.port WS-RPC server listening port (default: 8546)
--ws.api API's offered over the WS-RPC interface (default: eth,net,web3)
--ws.origins Origins from which to accept WebSocket requests
--ipcdisable Disable the IPC-RPC server
--ipcapi API's offered over the IPC-RPC interface (default: admin,debug,eth,miner,net,personal,txpool,web3)
--ipcpath Filename for IPC socket/pipe within the datadir (explicit paths escape it)
You'll need to use your own programming environments' capabilities (libraries, tools, etc) to connect via HTTP, WS or IPC to a geth node configured with the above flags and you'll need to speak JSON-RPC on all transports. You can reuse the same connection for multiple requests!

Note: Please understand the security implications of opening up an HTTP/WS based transport before doing so! Hackers on the internet are actively trying to subvert Ethereum nodes with exposed APIs! Further, all browser tabs can access locally running web servers, so malicious web pages could try to subvert locally available APIs!

Operating a private network
Maintaining your own private network is more involved as a lot of configurations taken for granted in the official networks need to be manually set up.
