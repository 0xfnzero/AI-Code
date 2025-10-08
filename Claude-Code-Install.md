Claude Code Install
1️⃣ 安装 Node.js（已安装可跳过）
确保 Node.js 版本 ≥ 18.0


# Ubuntu / Debian 用户
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo bash -
sudo apt-get install -y nodejs
node --version

# macOS 用户
sudo xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install node
node --version
🧭如果安装其他的 中转客户端cli 记得先卸载 （可选）
第一步：检查安装位置

检查是否在本地项目中安装


npm ls @anthropic-ai/claude-code
检查是否被全局安装


npm ls -g @anthropic-ai/claude-code
🧹 第二步：执行卸载操作

卸载本地安装的包


npm uninstall @anthropic-ai/claude-code
卸载全局安装的包


npm uninstall -g @anthropic-ai/claude-code
2️⃣ 安装 Claude Code

npm install -g @anthropic-ai/claude-code
claude --version
3️⃣ 开始使用
获取 Auth Token： ANTHROPIC_AUTH_TOKEN ：会单独发给你，类似格式： "cr_..."

API地址： ANTHROPIC_BASE_URL：给你的连接点url 是本站的 API 服务地址，与主站地址相同

在您的项目目录下运行：


cd your-project-folder
export ANTHROPIC_AUTH_TOKEN=cr_...
export ANTHROPIC_BASE_URL=给你的连接点url
claude 你是什么大模型
运行后 选择你喜欢的主题 + Enter 确认安全须知 + Enter 使用默认 Terminal 配置 + Enter 信任工作目录 + Enter

开始在终端里和你的 AI 编程搭档一起写代码吧！🚀

4️⃣ 配置环境变量（推荐）
环境变量

#Mac和Linux 环境变量
export ANTHROPIC_BASE_URL="连接点url"
export ANTHROPIC_AUTH_TOKEN="你的key"
cd your-project-folder
claude
Windows cmd命令行环境变量

set ANTHROPIC_BASE_URL=连接点url
set ANTHROPIC_AUTH_TOKEN=你的key
cd your-project-folder
claude
Windows PowerShell环境变量

$env:ANTHROPIC_BASE_URL="连接点url"; 
$env:ANTHROPIC_AUTH_TOKEN="你的key"
cd your-project-folder
claude
即可使用 Claude Code

为避免每次重复输入，可将环境变量写入 bash_profile 和 bashrc：
Mac或者Linux写到配置文件里面：

echo -e '\n export ANTHROPIC_AUTH_TOKEN=你的key' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_BASE_URL=连接点url' >> ~/.bash_profile
echo -e '\n export ANTHROPIC_AUTH_TOKEN=你的key' >> ~/.bashrc
echo -e '\n export ANTHROPIC_BASE_URL=连接点url' >> ~/.bashrc
echo -e '\n export ANTHROPIC_AUTH_TOKEN=你的key' >> ~/.zshrc
echo -e '\n export ANTHROPIC_BASE_URL=连接点url' >> ~/.zshrc
重启终端后，直接使用：


cd your-project-folder
claude
即可使用 Claude Code

