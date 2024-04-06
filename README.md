# Cisco ENCOR v1.1 Practice Labs
A collection of guided practice labs for the Cisco 350-401 ENCOR v1.1 exam.


## About the exam
According to Cisco's official **[certification listing](https://learningnetwork.cisco.com/s/ccnp-enterprise)**,
> (ENCOR 350-401) is a 120-minute exam associated with the CCNP and CCIE Enterprise Certifications. This exam tests a candidate's knowledge of implementing core enterprise network technologies, including dual stack (IPv4 and IPv6) architecture, virtualization, infrastructure, network assurance, security, and automation.

The exam itself consists of multiple choice, drag & drop, and hands-on lab questions.
It will behoove you to read the **[exam topics](https://learningnetwork.cisco.com/s/encor-exam-topics)** closely to get a sense of the level of depth required for each topic.
Topics starting with **Configure**, **Troubleshoot**, or **Verify** are likely to require configuration-level knowledge, whereas **Explain**, for instance, is more likely to appear as a multiple choice or drag & drop question.
However, keep in mind that while some topics may not show up as labs, you may still encounter configuration files or show commands as part of a multiple choice question.
That is to say, as much as possible... lab, lab, lab!


## What's in a lab?
Each lab contains:
- A respective Lab folder with:
    - A .yaml file for importing the starting configuration into CML.
    - A .md file with instructions for completing the lab.

- A respective Solution folder with:
    - A .yaml file representing a correctly configured lab state. (auto-grader work in progress!)
    - A .md file with answers to the lab instructions.


## How to Use
These labs are built for Cisco CML v2.6 and assume you have a running instance.

1. Navigate to your CML server via a web browser.



To load the topology, upload the selected lab .yaml file and proceed with the instructions for configuration.

