# 1Password Challenge Write Up

### ID

### Members

groozavu
ninjaslikecheese

### Hardware

| GPU                     | count |
|-------------------------|------:|
| NVIDIA GeForce GTX 1070 | 6     |

* Note: The costs are roughly rounded.

### Costs

| Cost                       | Amount    |
|----------------------------|----------:|
| 6 GPU mining rig: GTX 1070 | ~3000 EUR |
| 5 months Electricity bill  | ~500 EUR  |


### Software used
* Hashcat 4.2.1 & 5.0.0
* OS: EthOS
* Candidate generators: Python/Golang

With this hardware during the first couple of months we had around 47 kH/s, but after we figured out it was bottlenecking because of the CPU and RAM, we upgraded it, and it went to a steady 57 kH/sec.

### Writeup

The whole story started around mid August 2018. I saw the challenge on Bugcrowd, so since we already had the equipment that we used for mining, we decided to try something new, even though it was a very small chance for us to win. Basically the software set-up that we used 90% of the cracking time took around 2-3 weeks to build - mostly around 1 hour after work / day. 
When we decided to give this a try, after some calculations, we thought that for fun's sake we could just focus on hazard. Due to the fact that the keyspace is humongous We reasoned that randomly generating candidates would yield faster results than generating them one by one sorted alphabetically as long as there is enough entropy so that we don't have high duplicate rate. According to our calculations, we would need 1250 days to run through all the possible combinations: 18329^3 = 6,157,668,625,289 => 6157668625289/57000/86400 = 1250 days (keyspace/kH/seconds in a day).

First we experimented with different PRNGs, including BlumBlumShub, but then we realized that Python and any other scripting language are not fast enough to feed our hungry hashcat. At that point you guys made the hints available. Since we needed a way to generate passwords for a specific hint bit, modifying the actual Go code you guys provided looked like a good idea because it's a compiled language it should be quite fast, plus I'd also learn a bit of Go along the way. Just to give you guys a bit of context, since we had the basic rig (no HDD, just a USB flash drive), we had to use another server, which was mainly generating the candidates and was running a NFS share which got mounted on the hashcat machine. Along the way we tweaked the generating speed by adding Haveged, because generating candidates was quickly drying up the entropy pool. Since we generated ~ 800GB lists per round, we removed the duplicates from the same lists, but due to lack of disk space to store all files, we had to discard files on every run, in turn we surely had duplicated tries along the way. We reasoned that it's negligible, since the duplication rate / same wordlist was very low ~ 0.0011%.

When you guys released the second hints, we decided to focus on the hashes that have hint of `0b00`, since we had limited disk space, and the same wordlist might hold they answer to one or both of them - or not. The rest is history, not much to tell. I will however note the costs that we had along this cracking period, also including the mining rig hardware for reference, though it can be excluded from the calculus since we already owned it prior to starting.

We consider ourselves lucky to crack it in this time, since the keyspace is so huge, and I would like to note that the key here was that we had all the precise details. Including the wordlist from which the passwords were generated. Right at the beginning of the process, when I didn't read up all the docs, I thought the wordlist wasn't provided. Therefore, I tried to recreate it using lists of english words, which proved to be humongous, and it didn't provide a guarantee that it includes all the words. This proves how difficult it is to crack this kind of hash in a real world case scenario.

We gathered a lot of experience in the process and we want to thank 1Password for the challenge, and their commitement for improving the current password practices.
