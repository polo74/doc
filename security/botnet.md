# Botnet

This article talk about botnets and their technologies.

DISCLAIMER: this article is only here for a documentation purpose and is not supposed to be use for malintentionned actions

## PART 1: What is a botnet?

Generally associated with worms, a botnet (robot network) is network of compromised computers named "bots" controlled by an attacker through a C2 (Command and Control) server. 

The most of time, botnets are used to:
- launch DDoS attacks,
- launch phishing attacks,
- mining cryptocurrencies,
- or launch targeted attacks (botnet allow to hide the attacker identity)

```mermaid
graph TD;
C2-->BotA
C2-->BotB
C2-->BotC
C2-->BotD
C2-->...
BotA-->TargetA
BotA-->TargetB
BotB-->TargetB
BotC-->TargetB
BotD-->TargetB
```

We have to highlight that botnets are malicious and are associated to crime the most of time. But the botnets concept can be used in legal ways, as distributed computing (for example, [bioinc](https://boinc.berkeley.edu/) can be considered as a legal botnet)

While associated with worms, botnets are self-propagating.

## PART II: Botnets functionnalities

This part describe the main functionnalities present in botnets.

### Launch commands

The main functionnality of a botnet is to launch commands on a single, many or all the bots.

### Update bots

It is possible for the C2 to send updates to bots.  as the same way you can update a software. 
The update functionnality is important for 2 reasons:
- It allows to update and patch the bots, like a normal software, for example to add a new functionnality or add other propagation scripts,
- and it allows to modify the bots signature and bypass the antivirus checks.

## PART III: Botnets technologies

The goal of a malicious botnet is to be discrete and resilient. This is why they use different technologies to hide their bots and make the C2 server resilient.

### Peer to Peer

Peer to peer (P2P) is 

### Domain Generation Algorithm

Domain generation algorithm (DGA) is, as his name say, an algorithm which generate domain names. 

It takes a seed, 
