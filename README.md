# hyperledger

# inicializar ambiente
vagrant up
vagrant ssh
cd /hyperledger
bash install-hyperledger-composer.sh

# inicializar fabric
cd ~/fabric-dev-servers
export FABRIC_VERSION=hlfv12
bash startFabric.sh
bash createPeerAdminCard.sh

# inicializar composer-playground
composer-playground

# criar uma businessnetwork
cd ~/
yo hyperledger-composer:businessnetwork
cd tutorial-network/
composer archive create -t dir -n .
composer network install --card PeerAdmin@hlfv1 --archiveFile tutorial-network@0.0.1.bna
composer network start --networkName tutorial-network --networkVersion 0.0.1 --networkAdmin admin --networkAdminEnrollSecret adminpw --card PeerAdmin@hlfv1 --file networkadmin.card
composer card import --file networkadmin.card
composer network ping --card admin@tutorial-network

# inicializar o rest-server
composer-rest-server -c admin@tutorial-network -n always -w true

# stopar fabric
cd ~/fabric-dev-servers
bash stopFabric.sh

# limpar peeradmincard
# limpar development session 
cd ~/fabric-dev-servers/
bash teardownFabric.sh


# destroy a previous setup
docker kill $(docker ps -q)
docker rm $(docker ps -aq)
docker rmi $(docker images dev-* -q)
