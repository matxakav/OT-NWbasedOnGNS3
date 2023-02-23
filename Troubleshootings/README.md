# Troubleshootings

1. ✓ OpenSwitch not detect NICs
   - located the problem to KVM by controlling variants
   - move from EC2 instance to home desktop to solve the issue
2. ✓ ASAv access control list block DNS and Modbus/TCP
   - solved by reconfigure the ACLs
3. ✓ BGP between edge router and ISPs not transfer DNS query
   - solved by removing the ISPs and using single edge router with NAT
4. ✓ [System exception when image is uploaded](https://github.com/SCADA-LTS/Scada-LTS/issues/2473)
   - special thanks to Scada-LTS team to help troubleshooting the issue, solved with their support
5. ⦿ Cannot reproduce disguised LSA attack using frrouting and vIOS L2
