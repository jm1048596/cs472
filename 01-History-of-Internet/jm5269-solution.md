### Part 1: Problem-Solution Mapping Table

| Problem | Solution Proposed by Paper OR Why Not Addressed | How We See This Today |
|---|---|---|
| 1. Different Packet Sizes | Addressed: The packets would be broken down into small enough pieces to be received by the next network. They could be broken down multiple times before they reached their eventual destination. | The packets being broken down into smaller and smaller pieces probably causes significant latency compared to if everything was sent in one shot. |
| 2. Scale of the Internet | Not addressed: Computers hadn't even entered households yet in 1974. The authors were not designing these internetworks with the scale we know the internet has today in mind. | I've seen sites sometimes shut down if too much traffic is faced in too short a window. This kind of scale wasn't given much thought when this paper was written because computers weren't as widely accessible. |
| 3. Process-to-Process Communication | Addressed: Ports were created to solve this problem. A standard port number format was needed for this, which allowed the correct processes to receive incoming information over the network. | My computer and phone constantly receive notifications from many different places, so ports might be in use here to distinguish where messages are coming from. |
| 4. Knowing you're connected to the correct site (security/authenitication) | Not addressed: Websites didn't become a thing until more than 15 years after this paper was published. They were thinking at the basic process level, not at the grand scale that the world wide web exists at today. | I've accidentally mistyped a domain name multiple times in the past; this is a scary thing because you don't know where that url might take you. Some companies do purchase slighty-misspelled domain names for this reason. |
| 5. Reliability Across Multiple Networks | Addressed: TCP is a two-way stream; the message is sent from the sender, and the receiver subsequently sends a response in the reverse direction indicating how successfully they received the message. | I sometimes notice file downloads stall for no apparent reason. This is probably TCP at work: because every file of that download is needed, any failed retrieval is retried until successful. |
| 6. Spam/Abuse prevention | Not addressed: The authors were not as concerned with overuse of the networks. Computers weren't widespread at this point, so it's unlikely something like that were happen from their point of view. They were trying to answer the problem of unifying the networks. | DDoS (Distributed Denial-of-Service) attacks are a modern example of flooding a network. Too much traffic to one network or server causes it to shutdown temporarily. |

### Part 2: AI-Assisted Protocol Investigation

**A. Investigation Overview**

I chose the "Reliability Across Multiple Networks" problem to dive deeper into. Specifically, I was curious about how some online platforms like online games and video calls can buffer sometimes, but a file will always complete its download if allowed to. With TCP being strictly 2-way, shouldn't the games and video calls always complete their transmissions too?

**B. Key Questions You Asked**

- why does an online game or video call sometimes buffer or lag while something like a file download always completes?
- have they always used udp? has it been around since their inception?
- are there any platforms that could realistically use either tcp or udp? the examples given so far have seemed pretty decisive, but i'm wondering if there are some more "grey area" examples
- and what would happen in, say, that online multiplayer game example if udp were used and some error happened when saving the state of a player's inventory? could their whole inventory be at risk?
- how much latency does something like that full snapshot introduce? or more specifically, how do the developers decide where to draw the line when it comes to decisions like that?
- so to tie it all back together, it really is a case-by-case type of decision, the choice between tcp or udp (or a mix of both? it all depends on what it's individually being used for?


**C. Connection to 1974 Paper**

From what I learned through my conversation with the AI (which was a Claude session), it seems as though a lot of what Cerf-Kahn proposed in 1974 is still used today. TCP is very widely used across the internet 50 years later because of its reliability. A file download will always complete because it uses TCP; if something fails in the download, the receiver notifies the sender of the error and that specific part of the download is retried.

It was quickly realized, though, that TCP wasn't the best solution to every problem that would arise after Cerf-Kahn wrote their paper. Certain programs, like online multiplayer games and video calls, don't care as much about that two-way reliability that TCP guarantees; by the time the delayed packet reaches its destination, it will be stale and not needed by the user anymore. This is why UDP was created, which is a 1-way street for packets instead of a 2-way street. UDP allowed these services to prioritize speed as they weren't required to wait for the proper response from the recipient. This speed is, of course, much more important in video calls and multiplayer games, even if it comes at the cost of reliability.

**D. Surprise Finding**

The split between TCP and UDP made sense to me, but I was surprised to find that many services actually employ both protocols simultaneously throughout their programs. I hadn't considered that two protocols that have starkly different priorities in mind would be used in unison across the same code. It seems, though, that this combination of the two is what allows modern platforms to ensure both speed and reliability where needed. After taking all of what I learned into account, this combining of the two makes a lot of sense.

**E. AI Conversation Documentation**

ME: why does an online game or video call sometimes buffer or lag while something like a file download always completes?

AI: It comes down to which network transport protocol each application uses. File downloads use TCP, which guarantees every byte arrives and arrives in order — if a packet is lost, the receiver notices the gap and asks the sender to resend it, so the download pauses but always finishes complete. Games and video calls typically use UDP, which sends packets with no guarantee of delivery and no automatic resending. If a packet is lost, it's just gone. This is because a lost packet in a live game or call represents a moment in time — by the time a resend arrived, it would already be stale. It's more useful to skip it and move on than to freeze waiting for old data, which is why you see rubber-banding or skipped video frames instead of a clean pause.

ME: have they always used udp? has it been around since their inception?

AI: Not quite. In the early 1970s internetworking work, including the 1974 Cerf-Kahn paper, reliability and addressing were bundled into one protocol, an early version of what became TCP — there wasn't yet a separate lightweight alternative. The split happened around 1977-1978, when researchers working on real-time packet voice found that TCP's insistence on resending lost data was actively harmful for real-time audio. This pushed the design toward splitting the protocol into IP (addressing/routing only) and TCP (reliability, built on top of IP), with UDP introduced alongside as the minimal alternative with no added reliability. So UDP is a direct response to real applications hitting the limits of a reliability-first, one-size-fits-all protocol.

ME: are there any platforms that could realistically use either tcp or udp? the examples given so far have seemed pretty decisive, but i'm wondering if there are some more "grey area" examples

AI: Yes — several platforms use both at once for different parts of the same service. Video calls send live audio/video over UDP but typically use TCP for signaling (call setup, authentication). VoIP is similar: voice audio on UDP, call setup and chat on TCP. Web browsing is shifting too — traditional HTTPS uses TCP, but the newer HTTP/3 is built on QUIC, which runs over UDP, because TCP's strict ordering was slowing down pages with many simultaneous small requests. Live streaming platforms often use TCP-based protocols from streamer to server, but UDP-based delivery from server to viewers for low latency. Online multiplayer games often split traffic by type — movement and gunfire over UDP, chat and inventory over TCP or a TCP-like reliable channel.

ME: and what would happen in, say, that online multiplayer game example if udp were used and some error happened when saving the state of a player's inventory? could their whole inventory be at risk?

AI: Yes, that's a real risk with plain UDP. A dropped packet carrying an inventory update (like "picked up sword") would simply be gone, with no automatic recovery, potentially causing desync between client and server, and small losses could compound over time. This is why games generally don't leave that to raw UDP alone — they build a reliability layer on top of it, essentially reinventing part of what TCP does, but only for data that needs it. One common approach is periodic full-state snapshots: even if one update is lost, the next snapshot corrects the drift, trading extra bandwidth for protection against silent, permanent loss.

ME: how much latency does something like that full snapshot introduce? or more specifically, how do the developers decide where to draw the line when it comes to decisions like that?

AI: Snapshots don't add delay to individual packets so much as affect bandwidth and how quickly state re-syncs — the interval between snapshots sets the worst-case time a piece of state could be wrong before self-correcting. The real line-drawing question is: what's the cost of being wrong versus the cost of being slow? Fast-changing data like position is cheap to regenerate (the next update overwrites the stale one almost instantly), so speed wins. Rare, high-stakes data like a loot pickup isn't self-correcting if lost, so reliability wins even at the cost of a resend delay. Some game engines let developers tag each message as "reliable" or "unreliable" individually, choosing TCP-like or UDP-like behavior per message type rather than per protocol.

ME: so to tie it all back together, it really is a case-by-case type of decision, the choice between tcp or udp (or a mix of both? it all depends on what it's individually being used for?

AI: Exactly — the decision gets made per type of data, based on whether a delay while something gets fixed is worse than that data being silently lost or stale. Data where correctness matters more than speed (inventory, chat, purchases) leans toward TCP or a reliable layer; data where freshness matters more than perfection (position, live video/audio) leans toward UDP. Real platforms mix both constantly, sometimes within the same session. The original two-category framing (games glitch, downloads finish) turns out to be a simplified entry point into a much more granular, deliberate, message-by-message engineering trade-off.

### Part 3: Reflection

I found Cerf-Kahn's fragmentation solution to the packet size difference issue to be rather cool and ingenious. It's such a simple solution, yet something that worked so well at the time, and the gateway didn't even need to do all of the work. I was also able to learn much about more modern applications of Cerf-Kahn's proposals from the AI conversation, which is obviously something I couldn't have gleaned from the paper itself. I feel as though I'm able to appreciate how the different pieces of the internet are connected on a much finer level now thanks to this assignment. It feels good to be able to break such a large and imposing interface into its more granular pieces.