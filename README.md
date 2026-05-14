# proxmox-cybersecurity-lab
A documented home lab built on Proxmox VE, featuring an OPNsense firewall, VLAN segmentation, and a suite of offensive and defensive security tools for hands-on cybersecurity practice.

# Proxmox Cybersecurity Home Lab

A self-hosted cybersecurity lab running on a Proxmox VE hypervisor, built for 
hands-on practice in network security, threat detection, and offensive security 
techniques. The lab is fully segmented using pfSense and VLANs, and hosts a 
range of blue team and red team tooling across isolated network zones.

## What's in this lab

- **Hypervisor:** Proxmox VE on bare-metal hardware
- **Firewall/routing:** OPNsense with VLAN segmentation
- **Blue team:** Wazuh, Security Onion, TheHive, Cortex, Nessus
- **Red team:** Kali Linux, Caldera, Metasploit
- **Vulnerable targets:** DVWA, bWAPP, VulnHub machines
- **Infrastructure:** Windows Active Directory, Ubuntu/Docker, Portainer

## Purpose

This repository documents the full build process — from hardware setup and 
Proxmox installation through to firewall configuration and VM deployment — 
serving as both a personal reference and a portfolio of practical 
infrastructure and security work.

> ⚠️ This is a isolated home lab environment. No services are exposed to the 
> public internet.
