

wget https://nodejs.org/dist/v16.9.1/node-v16.9.1-linux-x64.tar.gz

mkdir -p /usr/local/nodejs 

tar -xvf node-v16.9.1-linux-x64.tar.gz -C /usr/local/nodejs

echo 'export PATH=/usr/local/nodejs/node-v16.9.1-linux-x64/bin:$PATH' >> ~/.bashrc

source ~/.bashrc

node -v

