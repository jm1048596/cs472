### Part 1: Problem-Solution Mapping Table

| Problem | Solution Proposed by Paper OR Why Not Addressed | How We See This Today |
|---|---|---|
| Different Packet Sizes | Addressed: The packets would be broken down into small enough pieces to be received by the next network. They could be broken down multiple times before they reached their eventual destination. | The packets being broken down into smaller and smaller pieces probably causes significant latency compared to if everything was sent in one shot. |
| Scale of the Internet | Not addressed: Computers hadn't even entered households yet in 1974. The authors were not designing these internetworks with the scale we know the internet has today in mind. | I've seen sites sometimes shut down if too much traffic is faced in too short a window. This kind of scale was never thought of when this paper was written. |
| Addressing Across Networks | Addressed: Ports were created to solve this problem. A standard port number format was needed for this, which allowed processes to distinguish between the potentially many different message streams being received at once. | My computer and phone constantly receive notifications from many different places, so ports might be in use here to distinguish where messages are coming from. |
| Knowing you're connected to the correct site (security/authenitication) | Websites didn't become a thing until more than 15 years after this paper was published. They were thinking at the basic process level, not at the grand scale that the world wide web exists at today. | I've accidentally mistyped a domain name multiple times in the past; this is a scary thing because you don't know where that url might take you. Some companies do purchase slighty-misspelled domain names for this reason. |
| Reliability Across Multiple Networks | Addressed: TCP is a two-way stream; the message is sent from the sender, and the receiver subsequently sends a response in the reverse direction indicating how successfully they received the message. | This is a classic example of lag in an online video game. The game will sometimes pause, and then jump around through different frames in an attempt to catch the player up. |
| Spam/Abuse prevention | Not addressed: The authors were not as concerned with overuse of the networks. Computers weren't widespread at this point, so it's unlikely something like that were happen from their point of view. They were trying to answer the problem of unifying the networks. | DDoS (Distributed Denial-of-Service) attacks are a modern example of flooding a network. Too much traffic to one network or server causes it to shutdown temporarily. |

### Part 2: AI-Assisted Protocol Investigation


### Part 3: Reflection