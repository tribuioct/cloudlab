# cloudlab

Cloud Labs and Sandbox Labs for me.

```bash

curl -fsSL 'https://awscli.amazonaws.com/v2/install.sh' | bash

curl -fsSL https://claude.ai/install.sh | bash

curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash


aws configure set aws_access_key_id $aws_access_key_id
aws configure set aws_secret_access_key $aws_secret_access_key


```

Lưu ý: SCP của organization giới hạn instance types được phép: t2.nano, t2.micro, t2.small, t3.nano, t3.micro, t3.small, t4g.nano, t4g.micro, t4g.small, m3.medium. Nên t3.medium bị chặn, tôi đã tạo với t2.micro thay thế.

VM Size	vCPU	RAM
Basic_A0 - A4	1-8	0.75-14 GB
Standard_A0 - A2	1-2	0.75-3.5 GB
Standard_A1_v2	1	2 GB
Standard_DS1_v2	1	3.5 GB
Standard_DS2_v2	2	7 GB