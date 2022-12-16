---
{"dg-publish":true,"topics":["MFA","2FA"],"type":"notes","permalink":"/sys-admin/keycloak/choosing-the-right-2-fa-method/","dgPassFrontmatter":true}
---

## Own notes

There are several different methods that can be used for 2FA, including:

1.  SMS-based 2FA: This method involves sending a one-time code via text message (SMS) to a user's phone, which the user must then enter in order to log in. This is a convenient option, but it is vulnerable to SIM-swapping attacks, in which an attacker gains control of the user's phone number and can intercept the 2FA codes.
    
2.  Authenticator app-based 2FA: This method involves using a smartphone app, such as Google Authenticator or Authy, to generate a one-time code. The user enters this code in order to log in. This is a more secure option than SMS-based 2FA, as it is not vulnerable to SIM-swapping attacks.
    
3.  Hardware token-based 2FA: This method involves using a physical device, such as a key fob or a USB stick, to generate a one-time code. The user enters this code in order to log in. This is a secure option, but it requires the user to carry the hardware token with them at all times.
    
4.  Biometric-based 2FA: This method involves using a biometric factor, such as a fingerprint or a facial scan, to authenticate the user. This is a convenient option, but there are several legitimate concerns to consider, including the accuracy and security of the system, potential privacy issues, and the convenience of using the system in different situations. It is important to carefully evaluate the potential drawbacks and limitations before implementing biometric authentication.

### Comparison of 2FA methods from a security point of view  
  
Overall, SMS-based 2FA is the least secure option, while hardware token-based 2FA is the most secure. Biometric-based 2FA should be avoided.  
  
**Authenticator app-based 2FA** is an intermediate option with trade-offs in terms of convenience and security: at least, it should be implemented with at-rest encryption as a measure against physical access to the device. The principal drawback is that you need your phone with you, enough battery and Internet connection. There are also physical solutions that provide OTP codes, with their own pros and cons.  
  
Biometric-based 2FA is not recommended, as it uses an immutable, non-deniable identifier as a secret, so there is no plausible deniability.  
  
As a consequence, it is straightforward for an adversary to circumvent it; the user doesn't even need to be conscious to have his finger put on a fingerprint reader or can hardly refuse to have his face scanned. A suitable approach would be to use biometrics as a substitute for a username (biometrics is something you are, not something you have nor know).  
  
**Hardware token-based 2FA** is the most secure approach, since an attacker would need both the password and physical access to the hardware token in order to gain access to the account, making it much more difficult to compromise. In addition, the hardware tokens can be protected with a PIN, which bring them [plausible deniability](https://en.wikipedia.org/wiki/Plausible_deniability).


## NIST Special Publications
[Digital Identity Guidelines](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63-3.pdf)
[Authentication and Lifecycle Management](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63b.pdf)]
[Enrollment and Identity Proofing](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63a.pdf)

>“Due to the risk that SMS messages may be intercepted or redirected, implementers of new systems SHOULD carefully consider alternative authenticators. If the out of band verification is to be made using a SMS message on a public mobile telephone network, the verifier SHALL verify that the pre-registered telephone number being used is actually associated with a mobile network and not with a VoIP (or other software-based) service. It then sends the SMS message to the pre-registered telephone number. Changing the pre-registered telephone number SHALL NOT be possible without two-factor authentication at the time of the change. OOB using SMS is deprecated, and may no longer be allowed in future releases of this guidance.
>
>“If out of band verification is to be made using a secure application (e.g., on a smart phone), the verifier MAY send a push notification to that device. The verifier then waits for a establishment of an authenticated protected channel and verifies the authenticator’s identifying key. The verifier SHALL NOT store the identifying key itself, but SHALL use a verification method such as **hashing** (using an approved hash function) or **proof of possession of the identifying key** to uniquely identify the authenticator. Once authenticated, the verifier transmits the authentication secret to the authenticator. Depending on the type of out-of-band authenticator, either: * The verifier waits for the secret to be returned on the primary communication channel.”

