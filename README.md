# **Senior Capstone: Security Awareness AI LLM Using Ollama, Open WebUI, and OpenVPN**

### I developed J.A.K.E (Judgement-based Awareness and Knowledge Expert), a custom cybersecurity assistant powered by a locally hosted LLM using Ollama, OpenWebUI, and an integrated OpenVPN Access Server for secure remote access and model management. J.A.K.E helps users identify suspicious messages, links, and files by explaining risks in clear, beginner-friendly language while maintaining a professional tone. I engineered the system prompt, behavioral rules, and conversational flow to ensure accurate threat assessment, safe guidance, and consistent refusal of non-cybersecurity queries. This project showcases my ability to design secure AI-driven tools, implement strong safety guardrails, manage remote access infrastructure, and tailor language models for practical cybersecurity awareness and education.

## Project Description
- My project is essentially a security awareness tool for end users in a fictional organization to utilize when they either receive suspicious emails and want to ask if the email is likely legitimate or malicious, OR if they have overall security questions. This is all in the effort of sparing the Information Technology and Information Security teams the time wasted on these questions to better align their efforts on more pressing matters in the organization.
- It is all hosted on one device, a Dell PowerEdge R630 1U Rack-mounted server. The system is running Ubuntu Server 22.04, has 16 Cores across 2 CPUs, 32GB of DDR4 RAM, and an Nvidia Tesla P4 Datacenter GPU with 8GB of VRAM.
- The system is secured by ONLY being accessible via SSH for administrative tasks, and for end users is ONLY accessible via an OpenVPN tunnel with 2FA via Microsoft Authenticator.
- The system is utilizing OpenWebUI in Docker for the front-end interface that users will interact with, making API calls to a local Ollama instance for the actual questions and queries. The underlying LLM used in the demo is mistral:latest.

## Phase One
- Phase One was much of my work figuring out how I wanted to have this system set up. Did I want to put everything all on one machine, or do I want to use multiple machines for this project? Should I virtualize using a system such as Proxmox to create individual containers for Ollama, OpenWebUI, OpenVPN, etc. or just use bare metal? I ultimately planned to work on bare metal with Windows 10 Professional.
- I initially had a VPN set up for other purposes that could have worked for my AI system, but I ultimately decided to create an entirely new VPN tunnel with MFA to further enhance the security of the system.
- For the GPU, I purchased an Nvidia Telsa P4 from eBay for about $100 a while back for tinkering with this locally and decided to keep this GPU for my new build. This project would be impossible without a dedicated GPU that fits in 1U, has enough VRAM for some basic models, and draws under 70W of power since I could not use dedicated power connectors for the server chassis, and the PCI-e rails deliver 70W. I could have used the Nvidia Tesla P4, which is the same form factor with a higher 16GB of VRAM, but it costs about $550-$650 and was not within my budget for the project.

## Phase Two
- Phase Two consisted of doing the initial work of trying to set this system up with Windows 10, which was a failure, because after I had the basic setup working (no VPN or WebUI yet, just Ollama) Windows decided to do an impromptu update, which broke my graphics driver for Ollama and broke Docker. Ouch!
- Moving on to Ubuntu Server 22.04! I was able to get everything set up for the operating system, Docker, OpenWebUI, Ollama, and the GPU Drivers were installed and set up successfully! I was finally able to utilize the system from localhost and the local network.
- Next steps were to get the VPN working so I can access the platform from the internet. I decided to use OpenVPN since it works flawlessly across all platforms and I have prior experience setting these up. I used OpenVPN Access Server since it allows for the easy setup of MFA, user provisioning/administration, and an intuitive, informative web interface.
- This is where I had the most issues with my project, trying to figure out why I could not access the VPN tunnel remotely, but I could access it locally. This obviously lends itself to some sort of port forwarding/firewall issue, but I checked both my Linux system and the network’s router settings to ensure that the default port (1194 UDP) was allowed access and port forwarded. I eventually gave up on 1194 and decided to set my VPN Tunnel’s port to 443 since that lines up with HTTPS and that traffic is always allowed, which made it work on the very first try!

## Phase Three
- For phase 3 I really focused on the actual AI system and the OpenWebUI interface themselves.
- For the OpenWebUI, I set the basic permissions for new users, set up accounts, and put some guardrails on what users were able to access.
- For the AI LLM, I initially decided to implement RAG in my system, so I chose a modified version of llama3 from Nvidia that was optimized for RAG. I very soon found out that the lack of documentation online for getting this RAG system working, and the limitations of my hardware would make this system impossible to function performantly. With my setup, I could either have RAG and the system responded extremely slowly, or I could work on refining a very powerful system prompt and have it work significantly faster.
- So, I scrapped the llama3 model and went with a smaller mistral model that worked significantly well with my setup.
I then focused on refining and developing a system prompt that gave me the output that I hoped for from the system. It took some time, but we got to a place where I feel relatively confident that users would be able to get informative data from this system!

<img width="1918" height="912" alt="image" src="https://github.com/user-attachments/assets/190c5f82-3bf5-4254-a75b-348dba250984" />

## Challenges Faced During the Project
### Successes
- I had trouble initially working on the VPN setup. After some testing and determination, I realized that my ISP was blocking the default port (1194) for OpenVPN and even port forwarding was not fixing this issue. I fixed this by routing my VPN tunnel via port 443 (HTTPS).
- I switched from Windows 10 to Ubuntu Server 22.04 and over the course of working on this project, marginally improved my skills with Linux.
- I changed my architecture to contain all relevant applications on to ONE machine, rather than the initial setup with the VPN on a different machine.
- Getting 2FA setup on my VPN was a major win for security on my system.
### Failures
- The RAG system was not working for me. 
- I was SEVERELY limited by the choice of GPU. These models require a LOT of VRAM and while I can scrape by at 8GB of VRAM, it would perform far better with something like 32-64GB of VRAM. Unfortunately, data center GPUs are incredibly expensive and having a 1U server also limits my available choices so I had to pick up the only GPU that would fit in my server (with some physical modifications).
- I initially wanted to work on a DNS server setup to give the IP Address to the OpenWebUI interface a better, cleaner URL, but decided to scrap it to focus on more important matters. 
- Don’t ever bother trying to deploy this on Windows 10. In the early phases of my project, I initially elected to go with Windows 10 for my server since I am extremely familiar with the GUI and OS, however Windows Update loves to destroy and break everything I have built up to this point, so we scrapped the Windows idea.

## Changes and/or Improvements I Would Make in the Future
- I could have spent more time working to understand the RAG system to better optimize or utilize it.
- If I had more time and investment into this system I would absolutely work on implementing the DNS system (such as Pi-Hole) to make the URL more accessible.
- Never do this on a windows instance.
- I would absolutely change into a 2U or 4U server chassis so I could fit some dedicated server GPUs in it and not be limited by the form factor of the system.
- GPU was a big limiter for this project, so I would get a significantly more powerful GPU to run larger models, or multiple models concurrently.

## Possible Enhancemnets
- Use a more current GPU to have access to better drivers and virtualization technologies.
- If this was to ship with laptops/workstations for the hypothetical organization, maybe just include it as a bookmark in the default browser for ease of access.
- Implement an SSO solution for accessing the OpenWebUI.
- More CPU Cores and RAM to focus on virtualization.
- Use a system such as Kubernetes alongside docker to automatically scale instances when demand/load is high.
  - (maybe utilize Azure/AWS/GCP at that point)
