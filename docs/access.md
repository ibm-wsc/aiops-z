# Accessing the Environment

!!! Important "Important - before you get started"

    Your experience may vary, but in past workshops, the virtual machines seemed to work best in a Firefox browser on your local workstation. Running the virtual machines in Chrome sometimes resulted in an issue where your mouse pointer is not visible.

    Regardless of which browser you use on your local workstation, within the virtual machine itself, Chrome has been set to the default browser and the lab works best within Chrome within the virtual machine.

    Within your virtual machine, the workshop website has been set as your home page within Chrome. Once you are successfully logged in to your virtual machine, it is recommended for your convenience that you stop accessing this site from your local workstation but instead access this site from within your virtual machine.

## Accessing your Virtual Machine
1. Go here: [https://techzone.ibm.com/my/workshops/student/67bde333ae3e9e263f4c586e](https://techzone.ibm.com/my/workshops/student/67bde333ae3e9e263f4c586e){target=_new}

    !!! Tip

        If it is more convenient for you, the above URL is represented by the following QR Code:  

        ![qrcode_techzone.ibm.com](qrcode_techzone.ibm.com.png)

2. If you are not currently logged in with your IBMid, you will be prompted to log in at a screen like below. If you do not already have an *IBMid*, the log in page should contain a link to allow you to create one.  They are free and it only takes a few minutes to create it. 

    ![IBMId-login](IBMId-login.png)

    !!! Tip
   
        If the log in page does not have an obvious link for creating an IBMid, try [here](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Flogin.ibm.com%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3FqsId%3Db9977aed-1e6b-4321-9b43-ee4365544452%26client_id%3DODllMDk4YzItMjgxOC00){target=_new}

3. You should reach the workshop page.  There will be a field where you are asked to enter the workshop password, as in this screen snippet:

    ![workshop-login](workshop-login.png)

    Enter the workshop password provided by your instructor. For security reasons, this password is not listed here.

4. Once you successfully enter the workshop password, you should be assigned an environment, and you will get to a screen with a large blue button at the bottom which will allow you to access your workshop virtual machine within your browser.

    !!! Important

        Take note of your environment number, as you should use that number as the last two digits of your userid for the lab.  For example, if you were assigned environment **03**, then you will use the userid **user03** throughout the labs.

5. If prompted, log into the Windows virtual machine with password: `IBMDem0s` (that's a zero). You may already be logged in when first accessing the VM.

## Links to platforms

!!! Tip

    These URLs are given within each lab section at the appropriate place.  They are repeated here for your convenience, in case you accidentally close your browser tab or window and lose your session- you can use the links here to easily resume your interrupted session.  

- OpenShift Cluster URL: [https://console-openshift-console.apps.atsocpd1.dmz/dashboards](https://console-openshift-console.apps.atsocpd1.dmz/dashboards){target=wsc_ocpconsole}
- Instana URL: [https://unit0-wsc.lcsins01.dmz](https://unit0-wsc.lcsins01.dmz){target=wsc_instana}
- Turbonomic URL: [https://nginx-turbonomic.apps.x2pn.dmz/app/](https://nginx-turbonomic.apps.x2pn.dmz/app/){target=wsc_turbo}
- IBM Cloud Pak for AIOps URL: [https://cpd-cp4aiops.apps.x2pn.dmz/zen/#/homepage](https://cpd-cp4aiops.apps.x2pn.dmz/zen/#/homepage){target=wsc_cp4aiops}

If you cannot access the webpage for any of the platforms above, check that the Cisco Secure Client VPN is logged in on the Virtual Machine. If it is no longer logged in, please let the lab administrator know.

Please do not log off or reboot the Virtual Machine, as that will disconnect the VPN.

## OpenShift, Instana, Turbonomic, and CP4AIOps credentials:

Usernames and passwords are the same for all of the platforms used in this tutorial.

!!! Note "second username for the Turbonomic lab"

    The Turbonomic lab uses two userids- the *usernn* listed below, plus a second userid *usernn-actions*, where 'nn' is your student number.  The password for this second userid is the same as that used for *usernn*. The Turbonomic lab instructions will tell you when to use each of these two userids.

| Environment ID | NN | Username | Password |
|-----|----|--------------------|--------------------|
| 1 | 01 | `user01` | `p@ssw0rd` |
| 2 | 02 | `user02` | `p@ssw0rd` |
| 3 | 03 | `user03` | `p@ssw0rd` |
| 4 | 04 | `user04` | `p@ssw0rd` |
| 5 | 05 | `user05` | `p@ssw0rd` |
| 6 | 06 | `user06` | `p@ssw0rd` |
| 7 | 07 | `user07` | `p@ssw0rd` |
| 8 | 08 | `user08` | `p@ssw0rd` |
| 9 | 09 | `user09` | `p@ssw0rd` |
| 10 | 10 | `user10` | `p@ssw0rd` |
| 11 | 11 | `user11` | `p@ssw0rd` |
| 12 | 12 | `user12` | `p@ssw0rd` |
| 13 | 13 | `user13` | `p@ssw0rd` |
| 14 | 14 | `user14` | `p@ssw0rd` |
| 15 | 15 | `user15` | `p@ssw0rd` |
| 16 | 16 | `user16` | `p@ssw0rd` |
| 17 | 17 | `user17` | `p@ssw0rd` |
| 18 | 18 | `user18` | `p@ssw0rd` |
| 19 | 19 | `user19` | `p@ssw0rd` |
| 20 | 20 | `user20` | `p@ssw0rd` |
