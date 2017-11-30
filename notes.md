# httpbis
Secondary Certificates (https://tools.ietf.org/html/draft-bishop-httpbis-http2-additional-certs)
- Motivation: 
	- TLS offers up at most one certificate per peer.
	- HTTP/2 prevents TLS renegotiation (no client certificate)
- Built on exported authenticators from TLS (1.3)
	- Allows endpoint to prove ownership of a arbitrary certificates at any time in the TLS connection 
	- May be passed in H2 frames, e.g., cert request and response frames
- Benefits:
	- CDNs that serve multiple origins
	- Allows for better coalescing
	- Possible option for encrypted SNI -- connect with generic certificate at the TLS layer, and specific certificate at H2 layer.
	- Allows servers to specify client certificates per request -- per-request certificates are not possible with certifiate-based client authentication in TLS
- Status: likely to be adopted as a WG item

Early data
- TLDR: clients add Early Data header, must retry early data in the event of specific failures
- No comments or blockers, moving forward to WGLC

# ntp/tictoc
NTS (https://github.com/dfoxfranke/nts)
- TLDR: use TLS to derive a shared secret, negotiate cookies and AEAD algorithms, and use the key and algorithm to authenticate NTPv4 packets
- Only client-server mode considered
    - No more DTLS tunneling, peer-to-peer synchronization, broadcast, or server monitoring modes
    - These may be considered in experimental documents
- Outstanding issues to be completed before next WGLC
    - State machine diagrams for client and the protocol overview
    - Considering normative requirements for cookie security
- Concerns: how are AEAD nonces generated? 
    - SIV modes, e.g., AEAD_AES_SIV_CMAC_256, are recommended to help deal with the problem
- Implementations: ??

# opsec
Transport encryption and its effect on the Internet (XXX)
- XXX

Separating crypto negotiation and communication (XXX)
- XXX

# tls
SNI encryption
- Motivation: client privacy, especially when coupled with DNS-over-TLS
- SNI (mis)use cases:
    - Certificate matching
    - Load balancing and routing without TLS termination
    - Relevant example: Turkey blocks traffic to Wikipedia using the SNI
    - Some TLS proxies use the SNI to determine if traffic should be intercepted or let through without issue
        - Youtube/Facebook data is permitted, health or medical application services are inspected
- Variety of existing proposals:
    - Combined ticket format
    - Client Hello tunneling in 0-RTT data
    - DNS record that specifies the SNI to use
        - DNS is not great since cached entries prevent the server from changing its private key at will
- Outcome: no consensus on approach, yet the problem persists

Middlebox issues
- Needed to prevent insecure fallback by malicious entities
    - High failure rates make it difficult for clients to distinguish between network issues and active attacks
- 1.3 failure rates MUST be within some small epsilon of 1.2 failure rates

Rollout plan
- Major implementations and servers should have -22 changes integrated in at most two weeks time and will report back

ATLS
- 

# taps
draft-trammell-taps-post-sockets-03:
- Unwritten principles:
    - Must be possible to use Post Sockets on one side and BSD on the other side
    - Carriers must use sane defaults and work without configurations or custom PSCs
        - Applications can override default system configurations if desired
        - How can the system defaults change?

# quic
Charter updates
- Everything exception HTTP is out of scope for application mappings
    - No DNS-over-QUIC until 2019 (at the earliest)

Spin bit design team
- Consensus: RTT measurements by spin bit do not introduce a new source of measurement for geolocating endpoints
    - IP-based geolocation services exist
    - Passive RTT measurements have too much noise
    - See: https://github.com/britram/trilateration/blob/paper-rev-1/paper.ipynb
- Design issue: spin bit doesn't capture the right data for spurious traffic

Draft updates
- Remove FNV1a hashing for cleartext integrity in favor of AEAD protection with a verison-derived key
- ACK timestamps removed
- Unidirectional and bidirectional state machines supported
- Simplification of integer encoding

Connection migration
- Current behavior: peers latch onto new address, if migration occurs, and limit data sent to the new address.
- Principles:
    - Probing and latching are separable events
    - Interface use is a local policy decision
    - Honor peer's interface decision (latch onto new peer address once probe succeeds)
- Look at MOBIKE for relevant threat model and suggestions

# dprive (bar BOF)
- Google showed a demo of their DNS-over-TLS implementation
    - Opportunistic mode supported by default in "Private DNS"
        - Stub probes resolved on 853 for TLS support
    - Strict mode allows users to enter a resolver FQDN
        - Resolver validation happens with normal system trust evaluation
        - All traffic blocked until the resolver is validated -- this can take a while
    - Not shipping yet
- Operational questions:
    - Did we get the right padding size?
    - Power performance vs DNS over TLS padding size -- what are the tradeoffs and what is the sweet spot?
- DNS over TLS server-side padding profile chosen it fits three responses in a single segment
- Some folks are working on iPhone apps to do this
- See also DNS-over-HTTPS WG: https://datatracker.ietf.org/group/doh/about/
    - Charter-drive ID: https://www.ietf.org/archive/id/draft-hoffman-dispatch-dns-over-https-00.txt

# saag
Improving randomness in security protocols
- Problem: defending against systemic system PRNG failures.
- TLDR: mix a signed value of a static string into the output of a CSPRNG.

# cfrg
SPAKE2: draft-irtf-cfrg-spake2-04.txt
- Resurrected, document slightly updated
- Used by Magic Wormhole: https://github.com/warner/magic-wormhole
- Consider using PAKEs to replace password-based authentiation in web apps

PKEX: https://tools.ietf.org/html/draft-harkins-pkex-04#section-7
- Problem: establish trust in "raw" public keys used in, e.g., TLS, IKE, etc.
    - Internally, proof of possession of corresponding private key
- PKEX (algorithmically):
    - (Exchange): Use PAKE to establish secure channel with a trusted identity
    - (Commit): Transfer raw public keys and proof-of-possession over channel
- No proof, yet several interoperable implementations 
