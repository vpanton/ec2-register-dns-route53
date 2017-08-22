# ec2-register-dns-route53
Script to create CNAME record pointing to EC2 instance's Public DNS record in Route53

# Installation

Create IAM Policy in AWS (edit <ZoneID> with your Hosted Zone ID in Route53):
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "route53:ChangeResourceRecordSets",
                "route53:GetHostedZone",
                "route53:ListResourceRecordSets"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:route53:::hostedzone/<ZoneID>"
            ]
        },
        {
            "Action": [
                "route53:ListHostedZones",
                "route53:ListHostedZonesByName"
            ],
            "Effect": "Allow",
            "Resource": [
                "*"
            ]
        }
    ]
}
```
Attach the policy from above to role for EC2 instance.

Next install cli53 from https://github.com/barnybug/cli53.
Installation is easy, just download the binary from the github releases page (builds are available for Linux, Mac and Windows):
https://github.com/barnybug/cli53/releases/latest

    $ sudo mv cli53-my-platform /usr/local/bin/cli53
    $ sudo chmod +x /usr/local/bin/cli53

Alternatively, on Mac you can install it using homebrew

    $ brew install cli53


Next create a directory where you will keep the config and shell script files (/opt/edc/route53). 

    $ git clone https://github.com/vpanton/ec2-register-dns-route53.git
    
Edit the config file with your relevant information, and chmod it with 600, and chmod the shell script with 700.

Now test running the script and you should see it create a CNAME record in Route53. 

Now create an entry in your crontab with @reboot to run the script when the system boots.

```
@reboot /opt/edc/route53/ec2-register-dns-route53/register-route53.sh >> /var/log/register-route53.log 2>&1
```

Now shutdown the server (not reboot) and start it back up, when the server boots back up it will pickup a new dynamic public IP address (unless your using an Elastic IP) and the cron job will update the CNAME record in Route53 with the new Public IP/DNS entry.

## More Info

[Blog post on author's site for more information.](http://www.evergreenitco.com/evergreenit-blog/2015/3/1/script-to-register-dynamic-ec2-public-ip-with-route53-in-aws)
