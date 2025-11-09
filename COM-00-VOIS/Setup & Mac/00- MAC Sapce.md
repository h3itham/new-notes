- Storage Problem 
	```bash 
	  sudo find -x /System/Volumes/Data* -type f -size +1G exec ls -lh {} \;
	  117  sudo find -x /System/Volumes/Data* -type f -size +1G -exec ls -lh {} \;
	  118  cd /System/Volumes/Data/Library/Application\ Support/Forcepoint/Neo/EP/
	  119  cd REDB
	  120  rm -f rawevents.db
	  121  sudo rm -f rawevents.db
	```
