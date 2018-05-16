#!/usr/bin/env bash

site=www.corkit.com.br
region=sa-east-1
policy_template=policy.template
policy_template_parsed=policy.json

#handle errors
check()
{
	if [ $1 -ne 0 ]; then
		echo "error: " $2
		exit
	else
		echo "done: " $2
	fi

}

#aws s3 rb s3://$site --force
#check $? "removing old bucket"

aws s3api create-bucket --bucket ${site} --region ${region} --create-bucket-configuration LocationConstraint=${region}
check $? "creating bucket"

#I have a feeling that this is needed in order to put-bucket-website to work.
sleep 5

aws s3api put-bucket-website --bucket ${site} --website-configuration file://website.json
check $? "enabling static website hosting on this bucket"

sed -e "s/\${site}/$site/" $policy_template > $policy_template_parsed
check $? "parsing policy template"

aws s3api put-bucket-policy --bucket ${site} --policy file://policy.json
check $? "apply policy to bucket"

aws s3 cp --exclude ".git/*" --recursive ./ s3://${site}/
check $? "copying static content to the bucket"

#Test
curl --fail -s -o /dev/null -I http://${site}.s3-website-sa-east-1.amazonaws.com/
check $? "curl $site"
