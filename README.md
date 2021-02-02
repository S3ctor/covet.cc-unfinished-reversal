# Unfinished reversal of the p2c covet.cc
This reversal will cover the protection of their product's image, the authentication, some driver communication and anything else I came across whilst reversing the p2c; i ended this project early because I wanted to do other shit but feel free to finish the driver reversal I'll link everything in the git.

# The Crack
c0gnito emulator bypasses their loader's authentication without trouble,
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/bypass_auth1.PNG) 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/inside.PNG)
cheat loads fine, here's the cheat being used https://streamable.com/0ar5w6. It's an average-shit cheat nothing too special as you can see. I'd assume that this method could be used to crack every product they have for sale currently I didn't gain access to anyother cheats they provide. Apex is currently upto date as of the 2nd of Feb 2021.

# Overall thoughts
Covet's products are fairly shit when comparing it to other p2c's in not only the cheating experience but the carelessness towards their products security. You can also tell by reading the cheats logic within IDA that the developer still employs similar tactics to that of a beginner. The pricing for this cheat is somewhat fair to maintain less of a user base, but as of now you can just get lifetime to all of their products for only a few dollars by exploiting their shitty authentication. 

# Authentication
Covet.cc utilizes a third party vendor https://c0gnito.cc/ to authenticate their users and manage access to their product. c0gnito itself is already extremely vulnerable so by using my c0gnito emulation https://github.com/S3ctor/c0gnito-authentication-emulation we can gain semi-permanent access to their product (semi at this stage due to the driver download being an external link to discord) I could also use the 1.0 version of the emulation due to the c0gnito.dll module being located in dir. Authentication summary basically their negligence towards their authentication is an extremely major weak point because of c0gnitos vulnerability. Their c0gnito 'secret key' 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/secret_key.PNG) 

# Image protection
They have chosen to use VMP 1.60-2.05  to virtualize their image; after dumping the image we get to get into the proper analysis of the loader. Although VMP is good enough to prevent quick byte patches to the image it doesn't do a whole lot to hinder the attacker from reversing the loader. Other than VMP none of the strings were encrypted, the actual cheat logic is contained within their loader so we don't have to bother with having to get it from the server; the drivers image as mentioned earlier is stored on discord 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/driver_url.PNG) 
https://cdn.discordapp.com/attachments/784216980424884234/797620961646084137/driver.sys
sadly for them this isn't the only way someone can obtain their driver image because to get their driver to the local machine they use URLDownloadToFileW to download the file then store it in C:\\Windows\\IME under the name u_arent_cool_for_getting_this_driver_id_like_to_see_you_reconstruct_communcation.sys to add to that this downloaded image isn't protected one bit and requires zero effort to analyse, I'm yet to send the driver image to BE/EAC to get tapped but you get the idea;
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/driver_dir.PNG)
I also ran my PE appropriator https://github.com/S3ctor/PE-appropriator to see if their loader contained any other images within it that could be of use; which it did contain iqvw64e.sys which we can assume is due to https://github.com/z175/kdmapper so their are now 3 ways to quickly and easily obtain their driver. Which isn't too ground breaking as it is clear the developer didn't put a whole lot of effort into keeping it out of the attackers hand. 
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/pe_appro.PNG)

# Driver communication
Starting off with the driver query types; they first initialize their hook on the driver control dispatch method to securely by using this method from UC https://www.unknowncheats.me/forum/anti-cheat-bypass/372215-driver-control-dispatch-hooking-method.html the way the logic it is by awaiting the process r5apex.exe to be active and then they initialize communication by setting a global bool in the driver to true which the drivers ioctl handler rely's on to complete the driver queries.
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/io_controller1.PNG)

(top image is usermode bottom image is kernel)

![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/driver_iohandler.PNG)
![](https://github.com/S3ctor/covet.cc-reversal/blob/main/assets/querys.PNG) 

Some of the query codes they use for the driver, I'm not 100% sure about init as I am finishing this writeup at a later date so I can't remember if I am right about that, due to this being a later writeup Ive kinda lost the reversal of the drivers read and write primitives so I cant send them but basically once you find the drivers ioctl handler then you should be able to find the primitives by using the query codes I found. (Also idk if Ive added but they use KDmapper to map the driver).
