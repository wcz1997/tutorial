# 三个脚本
setup.sh
# 脚本和执行步骤

## 1. 创建 `setup.sh`
```sh
cat << 'EOF' > setup.sh
#!/bin/bash

# 检查是否提供了参数
if [ $# -ne 1 ]; then
  echo "Usage: $0 <name>"
  exit 1
fi

# 获取参数
NAME=$1

# 保存name到文件
echo $NAME > ~/.track_name

# 安装依赖
sudo apt-get update
sudo apt-get install -y jq libssl-dev libudev-dev pkg-config zlib1g-dev llvm clang cmake make libprotobuf-dev protobuf-compiler git screen

# 安装 Go 1.22.3
wget https://go.dev/dl/go1.22.3.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.22.3.linux-amd64.tar.gz
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
source ~/.profile

# 安装 Node.js 18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# 克隆项目
git clone https://github.com/airchains-network/evm-station.git
git clone https://github.com/airchains-network/tracks.git

# 切换到v0.02标签
cd tracks
git checkout tags/v0.02
cd ..

# 执行evm-station的初始设置
cd evm-station
go mod tidy
/bin/bash ./scripts/local-setup.sh

# 创建screen窗口执行 local-start.sh
screen -S evm -d -m /bin/bash ./scripts/local-start.sh

echo "Setup script executed successfully. You can now run the second script."
EOF```
## 2. 创建 `create_wallet.sh`
