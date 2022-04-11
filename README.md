# ALB-Cognito-integeration-steps


From the AWS EC2 Console created a application load balancer:

![image](https://user-images.githubusercontent.com/41573564/158377345-6aed0c0f-c110-43ad-8825-1d286dd234af.png)

While creating the Load balancer we need to create target groups to define the listener function, in our case it should be our application.
For testing purpose used lambda function to get a HTTPS response in JSON format, used sample lambda function from AWS document site.


![image](https://user-images.githubusercontent.com/41573564/158387377-a5beb1dd-aafe-4ed4-ad55-f3515cb1c525.png)

Below is the lambda code:

console.log('Loading function');

exports.handler = async (event, context) => {
    let response = {
        "isBase64Encoded": false,
        "statusCode": 200,
        "statusDescription": "200 OK",
        "headers": {
            "Set-cookie": "cookies",
            "Content-Type": "application/json"
        },
        "body": JSON.stringify(event, null, 2)
    };
    return response;  
};

 Created a SSL signed certificate from https://www.samltool.com/self_signed_certs.php, this is mandatory as it is HTTPS call. 
 
 Once load balancer is set with the listenenr and target groups as lambda function, can try accessing it with load balancer DNS name. As given in below pic:
 
 ![image](https://user-images.githubusercontent.com/41573564/158388472-6926b597-20db-4ec4-ba0a-a0b908a293f3.png)
 
 Now load balancer works properly and reorutes the requests to lambda function. Now need to integrate cognito to authenticate all the requests coming to the ALB.
 
 From EC2 console created default cognito user pool and created a domain name :
 
 ![image](https://user-images.githubusercontent.com/41573564/158389143-1a60165f-b5d5-4caa-bc01-47594b2e714c.png)


![image](https://user-images.githubusercontent.com/41573564/158388922-a0d110e6-04c3-4562-9168-864e9f9d764b.png)

Created a default OIDC client in cognito from App Clients option in cognito:

![image](https://user-images.githubusercontent.com/41573564/158389332-7a411fda-4b80-4d32-9a52-eb729ea6c7df.png)

Modified listener lambda  function rule to authenticate with cognito using the userpool and the app client name:

![image](https://user-images.githubusercontent.com/41573564/158389709-c9bf1d30-4cf4-4145-ab37-56bded508b2b.png)

In app client settings in the cognito page added the call back URL as ALB dns name and  created the authorization methods like phone, email:

![image](https://user-images.githubusercontent.com/41573564/158390058-b8557f4b-6444-4d6d-a9d7-d4933bd3c21d.png)

Referenced AWS documentation and used below section to create callback url:

![image](https://user-images.githubusercontent.com/41573564/158390658-a30cf70c-f28c-49a3-88f7-f0139793f676.png)

so my callback URL was ALB DNS followed by /oauth2/idpresponse

https://testalbcog-1026837687.us-east-1.elb.amazonaws.com/oauth2/idpresponse

Tried accessing and got below page to authenticate:
![image](https://user-images.githubusercontent.com/41573564/158391326-209ec5d4-fc73-4496-ae6b-fd868a60fcf5.png)

Created groups and users in cognito user pool and then tried accessing the page:

U - testcog1
P - Testcog!23 


Got below JSON page from the lambda function:

![image](https://user-images.githubusercontent.com/41573564/158391641-f90814b1-b3f0-4ada-9ef6-4d9d8f449627.png)


This is how testing was performed for a basic ALB and cognito integration! :) 














 
 
