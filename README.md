sme-server
==========

recycle bin


Enable recycle bin
---------------------------------------------------
db accounts setprop accounts RecycleBin enabled
db accounts setprop accounts KeepVersions enabled 
signal-event ibay-modify accounts
db configuration setprop smb RecycleBin enabled
db configuration setprop smb KeepVersions enabled
/sbin/e-smith/expand-template /etc/samba/smb.conf
/etc/rc7.d/S91smb restart




samba config location
-----------------------
\etc\samba\smb.conf


Clean Recycle bin every 30 days
----------------------------------
#!/bin/bash

# ensure finds includes hidden files in the Recycle Bins
shopt -s dotglob

echo "+------------------------------------------------------------------------------+"
echo "|                           Samba recycle-bin cleaner                          |"
echo "+------------------------------------------------------------------------------+"

URF=$(find /home/e-smith/files/users/*/home/Recycle\ Bin/* -type f -ctime +30)
if [ "$URF" !=  "" ]
then
    printf "| %-76s |\n" "The following user recycle bin files were deleted:"
    IFS=$'\n'
    for file in $URF
    do
        printf "| - %-74s |\n" "$file"
        rm -f "$file"
    done
else
    printf "| %-76s |\n" "There were no old user recycle bin files to delete."
fi

echo "+------------------------------------------------------------------------------+"

IRF=$(find /home/e-smith/files/ibays/*/files/Recycle\ Bin/* -type f -ctime +30)
if [ "$IRF" !=  "" ]
then
    printf "| %-76s |\n" "The following ibays recycle bin files were deleted:"
    IFS=$'\n'
    for file in $IRF
    do
        printf "| - %-74s |\n" "$file"
        rm -f "$file"
    done
else
    printf "| %-76s |\n" "There were no old ibays recycle bin files to delete."
fi

echo "+------------------------------------------------------------------------------+"

URD=$(find /home/e-smith/files/users/*/home/Recycle\ Bin/* -type d -empty)
if [ "$URD" != "" ]
then
    printf "| %-76s |\n" "The following users recycle bin directories were deleted:"
    IFS=$'\n'
    for folder in $URD
    do
        printf "| - %-74s |\n" "$folder"
        rm -rf "$folder"
    done
else
    printf "| %-76s |\n" "There were no old user recycle bin directories to delete."
fi

echo "+------------------------------------------------------------------------------+"

IRD=$(find /home/e-smith/files/ibays/*/files/Recycle\ Bin/* -type d -empty)
if [ "$IRD" != "" ]
then
    printf "| %-76s |\n" "The following ibays recycle bin directories were deleted:"
    IFS=$'\n'
    for folder in $IRD
    do
        printf "| - %-74s |\n" "$folder"
        rm -rf "$folder"
    done
else
    printf "| %-76s |\n" "There were no old ibays recycle bin directories to delete."
fi

echo "+------------------------------------------------------------------------------+"


----------------------------------------------------------------------------------------------------------

crontab define
--------------

crontab -l
crontab -e
6 0 * * /root/recycle_clear

recycle_clear is the file in root dir


