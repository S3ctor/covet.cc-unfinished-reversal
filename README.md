# Reversal of the p2c covet.cc
This reversal will cover the protection of their product's image, the authentication, driver communication and anything else I came across whilst reversing the p2c.

# Authentication
Covet.cc utilizes a third party vendor https://c0gnito.cc/ to authenticate their users and manage access to their product. c0gnito itself is already extremely vulnerable so by using my c0gnito emulation https://github.com/S3ctor/c0gnito-authentication-emulation we can gain semi-permanent access to their product (semi at this stage due to the driver download being an external link to discord) I could also use the 1.0 version of the emulation due to the c0gnito.dll module being located in dir. Authentication summary basically their negligence towards their authentication is an extremely major weak point because of c0gnitos vulnerability.

# Image protection
They have chosen to use VMP 1.60-2.05  to virtualize their image; after dumping the image we get to get into the proper analysis of the loader. Although VMP is good enough to prevent quick byte patches to the image it doesn't do a whole lot to hinder the attacker from reversing the loader. Other than VMP none of the strings were encrypted, the actual cheat logic is contained within their loader so we don't have to bother with having to get it from the server; the drivers image as mentioned earlier is stored on discord 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/driver_url.PNG) 
https://cdn.discordapp.com/attachments/784216980424884234/797620961646084137/driver.sys
sadly for them this isn't the only way someone can obtain their driver image because to get their driver to the local machine they use URLDownloadToFileW to download the file then store it in C:\\Windows\\IME under the name u_arent_cool_for_getting_this_driver_id_like_to_see_you_reconstruct_communcation.sys to add to that this downloaded image isn't protected one bit and requires zero effort to analyse; due to this I took his challenge to reverse his driver communication a bit later in this write up. 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/driver_dir.PNG)
I also ran my PE appropriator https://github.com/S3ctor/PE-appropriator to see if their loader contained any other images within it that could be of use; which it did contain iqvw64e.sys which we can assume is due to https://github.com/z175/kdmapper so their are now 3 ways to quickly and easily obtain their driver. Which isn't too ground breaking as it is clear the developer didn't put a whole lot of effort into keeping it out of the attackers hand. 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/pe_appro.PNG)

# Driver communication
