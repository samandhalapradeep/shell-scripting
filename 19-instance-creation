#!/bin/bash

# Variables
AMI_ID=ami-0b4f379183e5706b9
SG_ID=sg-0463541756967fe45
INSTANCES=("mongodb" "redis" "mysql" "rabbitmq" "catalogue" "user" "cart" "shipping" "payment" "dispatch" "web")
availability_zone="us-east-1a"
ZONE_ID=Z00838311Q9EXSVA8UNJP
DOMAIN_NAME="pradeepstore.online"

        for i in "${INSTANCES[@]}"
        do
            echo "instance is: $i"
            if [ $i == "mongodb" ] || [ $i == "mysql" ] || [ $i == "shipping" ]
            then
            INSTANCE_TYPE="t3.small"
            else
            INSTANCE_TYPE="t2.micro"
    fi

    # Create EC2 instance
    IP_ADDRESS=$(aws ec2 run-instances --image-id ami-0b4f379183e5706b9 --instance-type $INSTANCE_TYPE \
    --security-group-ids sg-0463541756967fe45 --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=$i}]" \
    --query 'Instances[0].PrivateIpAddress' --output text)
    echo "$i: $IP_ADDRESS"

 #create R53 record, make sure you delete existing record
    aws route53 change-resource-record-sets \
    --hosted-zone-id $ZONE_ID \
    --change-batch '
    {
        "Comment": "Creating a record set for cognito endpoint"
        ,"Changes": [{
        "Action"              : "UPSERT"
        ,"ResourceRecordSet"  : {
            "Name"              : "'$i'.'$DOMAIN_NAME'"
            ,"Type"             : "A"
            ,"TTL"              : 1
            ,"ResourceRecords"  : [{
                "Value"         : "'$IP_ADDRESS'"
            }]
        }
        }]
    }
        '
  done