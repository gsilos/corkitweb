# rebuild steps

aws s3 rb s3://corkit --force
aws s3api create-bucket --bucket corkit --region sa-east-1 --create-bucket-configuration LocationConstraint=sa-east-1
aws s3api put-bucket-website --bucket corkit --website-configuration file://website.json
aws s3api put-bucket-policy --bucket corkit --policy file://policy.json
git clone https://github.com/gsilos/corkitweb.git
aws s3 cp --exclude ".git/*" --recursive /Users/Fabiano/x/corkitweb s3://corkit/

#http://corkit.s3-website-sa-east-1.amazonaws.com/
~                                                    
