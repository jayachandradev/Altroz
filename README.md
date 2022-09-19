# AppScan Source for Automation with Azure DevOps sample - Overview

This integration works with Azure DevOps (should work for both Cloud and on-prem versions of ADO) and it calls on a "self-hosted agent" to trigger AppScan Source CLI to run automated scan on a code repo located in Github. The key to this integration is the use of the self-hosted agent, where we can have the full AppScan Source suite installed and thus allowing us to run appscansrccli in its entirety. This integration also extracts the PDF report generated by AppScan Source and publish it in Azure DevOps - thus allowing users to view a full SAST report directly within Azure DevOps. 

## Demo workflow
1. Azure DevOps (ADO) checks out source code from Github Repo
2. ADO initiates the self-hosted agent that has AppScan Source Automation installed (I was using my Windows 2016 AWS instance for my demo, but you can use any machines you own)
3. ADO pushes the source code onto the agent
4. ADO runs the appscanscrccli command scripts, which including setting up an application, running a scan, generate assessment (ozasmt), generating PDF reports
5. PDF report is extracted out of the agent and is published back in ADO for viewing

![Demo Workflow Diagram drawio](https://user-images.githubusercontent.com/5158535/142063443-8c91ad4d-2261-4a56-b57c-21af87c21371.png)


I've made my Azure DevOp pipeline public and you can view it here:
https://dev.azure.com/ChiuAntony/AppScan%20Source%20Automation%20via%20self-hosted%20Agent

You can view the Azure DevOps YAML pipeline script in this repo, located here:
https://github.com/antonychiu2/AltoroJ/blob/master/azure-pipelines.yml

Click on the latest run:
![image](https://user-images.githubusercontent.com/5158535/140100674-6ac08195-7625-4b48-81ed-87fc79f563d6.png)


Click on the "1 published" for the report:
![image](https://user-images.githubusercontent.com/5158535/140100709-4a708247-66c8-4806-9c17-c94d58d37059.png)


The full report can be downloaded there:
![image](https://user-images.githubusercontent.com/5158535/140100743-99de1f84-cb55-42f0-aee5-5b5e037fd9c4.png)


** For the Appscan source script to run properly we need cli.token, make sure you have your cli.token in your local**
** In your local while running AppScanSrcCli Use "login server user_id password [-persist] [-acceptssl]" which will generate a CLI Token for login **
## Additional Notes
The following 4 files in the repo are required:

* __azure-pipelines.yml__ <--- This is the pipeline configuration, notice in line 18, we call appscansrccli to run "cli_script.txt". Also notice line 9: (pool: 'Default') is the agent pool where my self-hosted 2016 windows server machine is located. 
* __cli_script.txt__ <--- Runs AppScan Source CLI, will point to the .paf and ppf files to initialize the application. In this file make sure you edit login command "login **ASE Server ** -acceptssl" accordingly. 
* __AltoroJ_3.2.paf__ <---- Need to be pre-generated in AppScan Source for Analysis (think of this as putting in the appscan-config.xml in the context of ASoC)
* __AltoroJ_3.2.ppf__ <---- Need to be pre-generated in AppScan Source for Analysis (think of this as putting in the appscan-config.xml in the context of ASoC)


Finally, here are the instructions on how to setup a windows self-hosted agent:
https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-windows?view=azure-devops


