### Establishing a Secure Connection Using HTTPS and SSL/TLS

When establishing a secure connection using HTTPS, additional steps are involved compared to a standard HTTP connection. These steps ensure the authenticity and integrity of the communication by using SSL/TLS (Secure Sockets Layer/Transport Layer Security). Here's a detailed process of how a browser checks for site certificates and establishes a secure connection, focusing on where this fits into the TCP/IP model.

#### Steps to Establish a Secure HTTPS Connection

1. **Application Layer**: Initiating the Connection
   - The user types `https://example.com` into the browser.
   - The browser constructs an HTTPS request, indicating that a secure connection is required.

2. **Transport Layer**: Establishing the TCP Connection (Three-Way Handshake)
   - The browser (client) initiates a TCP connection with the server using the standard three-way handshake:
     1. **SYN**: Client sends a TCP segment with the SYN flag set to the server.
     2. **SYN-ACK**: Server responds with a TCP segment with both SYN and ACK flags set.
     3. **ACK**: Client sends a TCP segment with the ACK flag set, completing the handshake.

3. **Application Layer**: Starting the SSL/TLS Handshake
   - After the TCP connection is established, the browser begins the SSL/TLS handshake to set up a secure connection.
   - This process involves several steps to ensure the server's identity and establish encryption parameters.

#### SSL/TLS Handshake Process

1. **Client Hello**:
   - The browser sends a "Client Hello" message to the server, which includes:
     - Supported SSL/TLS versions.
     - Cipher suites (encryption algorithms) supported by the client.
     - Random data for session key generation.
     - Optional session ID for session resumption.

2. **Server Hello**:
   - The server responds with a "Server Hello" message, including:
     - Chosen SSL/TLS version.
     - Chosen cipher suite.
     - Random data for session key generation.
     - Session ID.
   - The server also sends its digital certificate, which contains the server's public key and is signed by a Certificate Authority (CA).

3. **Certificate Verification**:
   - The browser verifies the server's digital certificate by:
     - Checking the certificate's validity period.
     - Ensuring the certificate is signed by a trusted CA.
     - Verifying the certificate's chain of trust, leading up to a trusted root CA certificate stored in the browser or operating system.
     - Ensuring the domain name in the certificate matches the domain requested.

4. **Server Key Exchange (optional)**:
   - Depending on the chosen cipher suite, the server may send additional key exchange parameters.

5. **Certificate Request (optional)**:
   - The server can request a certificate from the client for mutual authentication.

6. **Client Key Exchange**:
   - The client generates a pre-master secret (a random value) and encrypts it with the server's public key (from the certificate).
   - The encrypted pre-master secret is sent to the server.

7. **Generating Session Keys**:
   - Both the client and the server use the pre-master secret and the random data exchanged earlier to generate the session keys (symmetric keys) for encrypting the data.

8. **Finished Messages**:
   - Both the client and the server send "Finished" messages to each other, encrypted with the session keys.
   - These messages confirm that the handshake was successful and that future messages will be encrypted.

9. **Secure Connection Established**:
   - The browser and server can now securely exchange data using the symmetric session keys.

### Summary of the Process in the TCP/IP Model Context

- **Application Layer**:
  - Initiates the HTTPS request.
  - Conducts the SSL/TLS handshake to establish a secure connection.
- **Transport Layer**:
  - Establishes the initial TCP connection using the three-way handshake.
- **Internet Layer**:
  - Routes the IP packets between the client and server.
- **Network Interface Layer**:
  - Transmits the data frames over the physical network.

### Hierarchy of Certificate Validation

1. **Leaf Certificate**: The server's certificate presented to the client.
2. **Intermediate Certificates**: Certificates that link the server's certificate to the root certificate.
3. **Root Certificate**: A trusted certificate stored in the browser or operating system, issued by a trusted Certificate Authority (CA).

The browser uses this hierarchy to verify the chain of trust from the server's certificate up to the trusted root certificate, ensuring the server's identity is legitimate and that the connection can be secured.

### Diagram of the Process

```plaintext
User -> Browser -> HTTPS Request -> TCP 3-Way Handshake -> SSL/TLS Handshake
  Client Hello -> Server Hello + Certificate -> Certificate Verification
  -> (Optional: Server Key Exchange, Certificate Request)
  -> Client Key Exchange -> Generate Session Keys -> Finished Messages
  -> Secure Connection Established -> Encrypted Data Exchange
```

By following these steps, the browser ensures a secure connection to the server, protecting data integrity and confidentiality during communication over the internet.