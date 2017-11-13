# Monday 11/13/17

## httpbis

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

## ntp/tictoc

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
- Implementations: ???





