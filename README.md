# aws-ec2-cicd-startstop-apache 🐳

![Stars](https://img.shields.io/github/stars/tquangdo/aws-ec2-cicd-startstop-apache?color=f05340)
![Issues](https://img.shields.io/github/issues/tquangdo/aws-ec2-cicd-startstop-apache?color=f05340)
![Forks](https://img.shields.io/github/forks/tquangdo/aws-ec2-cicd-startstop-apache?color=f05340)
[![Report an issue](https://img.shields.io/badge/Support-Issues-green)](https://github.com/tquangdo/aws-ec2-cicd-startstop-apache/issues/new)

![detail](screenshots/detail.png)

## reference
[youtube](https://www.youtube.com/watch?v=KoNWlnx6E1I)

## AWS IAM Role
1. ### for EC2
    - role name=`DTQRoleEC2`
    - policies=`AmazonS3FullAccess`(⚠️⚠️IMPORTANT⚠️⚠️: for CodeDeploy agent!!!) & `AmazonEC2RoleforAWSCodeDeploy`
1. ### for CodeDeploy
    - role name=`DTQRoleCodeDeploy`
    - policies=`AWSCodeDeployRole`

## AWS EC2
- tag name=`DTQEC2CICD`
- role=`DTQRoleEC2`
- bootstrap bash=
- `Choose User Data: for installing required packages.`
1. from: https://github.com/prabhakar2020/aws_code_deploy#steps-for-aws-code-deploy-using-s3-as-source
2. from: https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html
> - check CMD if ERR:
> - sudo service codedeploy-agent status
> - systemctl status codedeploy-agent.service
> - tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log


## AWS CodeDeploy
1. ### application
    - application name=`DTQCodeDeployCICD`
    ![codedepl1](screenshots/codedepl1.png)
1. ### deployment group
    - group name=`DTQCodeDeployGrpCICD`
    - role=`DTQRoleCodeDeploy`
    - EC2=`DTQEC2CICD`
    ![codedepl2](screenshots/codedepl2.png)
    - uncheck `Enable load balancing`

## AWS CodePipeline
[cicd](https://docs.google.com/spreadsheets/d/1m8LuBQUzqIQ0bJYb3NJZOT4iSb78xUEG/edit#gid=1878281573&range=B3)
1. ### config
    - name=`DTQPipelineCICD`
    - add src:
    > src from fork repo: `https://github.com/tquangdo/aws_codedeploy_using_github`
    ---
    ![addsrc](screenshots/addsrc.png)
    - add build: `skip`
    - add deploy:
    ![adddeploy](screenshots/adddeploy.png)
    - need to wait for deploying for quite long time
    ![wait](screenshots/wait.png)
    > if select `S3` instead of `github`
    - select S3 object=`https://github.com/tquangdo/aws_codedeploy_using_github/blob/master/SampleApp_Linux.zip`
    - with `SampleApp_Linux.zip` get by `curl -O http://s3.amazonaws.com/aws-codedeploy-us-east-1/samples/latest/SampleApp_Linux.zip`
    ![bys3](screenshots/bys3.png)
1. ### result
    1. #### NG!!!
        - ERR:
        ![err](screenshots/err.png)
        - reason: `wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install` can NOT install code deploy agent due to missing `AmazonS3FullAccess` policy!!!
        ```rb
        ...
        res = run_command(cmd, 'codedeploy-agent', 'status') // src code in wget URL
        ```
    1. #### OK (💣💣💣!!!)
        ![ok](screenshots/ok.png)
        - change `https://github.com/tquangdo/aws_codedeploy_using_github/blob/master/index.html`
        ```html
        <h1>Waaw, Congratulations Prabhakar</h1>
        ->
        <h1>DTQ!!!</h1>
        ```
        - pipeline will auto refresh CICD
        ![refresh](screenshots/refresh.png)
        ---
        ![ok2](screenshots/ok2.png)

## note: all provider list in deploy stage
1. ### CloudFormation
    - action mode has 4 items: `create/update stack` & `delete stack` & `replace stack` & `create/replace change set` & `execute change set` 
    ![n_cformation](screenshots/n_cformation.png)
1. ### CodeDeploy
    - this repo! (more additional 2 items: `Input artifacts` & `Variable namespace`)
    ![n_codedeploy](screenshots/n_codedeploy.png)
1. ### EB
    ![n_eb](screenshots/n_eb.png)
1. ### ECS
    ![n_ecs](screenshots/n_ecs.png)
1. ### ECS blue green
    1. #### AWS console
        ![n_ecs_bg](screenshots/n_ecs_bg.png)
    1. #### reference
        [cicd](https://docs.google.com/spreadsheets/d/1m8LuBQUzqIQ0bJYb3NJZOT4iSb78xUEG/edit#gid=1878281573&range=B4)
        - REF: the steps are explained in: [medium](https://medium.com/@shashank070/in-my-previous-blog-i-have-explained-how-to-do-initial-checks-like-code-review-code-build-cddcc21afd9f)
        ![bg_ref2](screenshots/bg_ref2.jpeg)
1. ### S3
    ![n_s3](screenshots/n_s3.png)
