---
layout: post
title: "Identify \"phishing\" Emails"
categories: [phishing, email, fraud]
feature-img: "img/identify_phishing_email.jpg"
---

"Phishing" refers to fraudulent attempts to obtain sensitive information, such as account 
passwords or banking and credit card information. At first glance, these emails look like they 
are coming from legit companies and individuals, but they're not.

As a general rule of thumb, never put account passwords, bank or credit card information, and 
extensive personal information in an email. Most companies have non-solicitation policies and 
will never ask for this type of information in an email.

If you've receive an email that you're not sure about, the below tips can help identify 
phishing attempts.

## Find out who really sent the email

View the email headers to see where an email really came from. For instructions on how to view 
headers in your email client visit [MxToolBox][email-headers]. The typical email header has 
several lines that start with "Received." Look at the last "Received" line; it should look 
something like the following:

{% highlight bash %}
Received:  from mta.email.example.com (172.16.10.104)
{% endhighlight %}

If the "**Received:  from**" information does not match the email address of the person or 
company that sent the email, it is probably a phishing attempt. If you need help analyzing your 
email headers, you can copy and paste your email headers into MxToolBox's [Email Header Analyzer][header-analyzer].

## Be cautious of links in emails

A common phishing attack technique is to add links in emails that look like they go to genuine 
websites. When taking a second look, the links will actually take you to web pages that have 
been replicated from legitimate company websites.

Most email clients help you identify these links; mouse over (**don't click**) any link in an 
email, and you will see a pop-up of the actual URL.

![Malicious Email Link]({{ site.url }}/img/malicious_email_link.jpg)

## Check the email greeting

Phishing emails usually start with generic greetings like "Dear valued customer," your email 
address, or account name, such as "Dear pookie123," instead of your actual name. Most legit 
companies will include your name in their email communication.

Another type of phishing email is a "spear phishing" attack, these types of emails are targeted 
to the recipient and may contain personal information. Attackers are able to gather information 
on a target by using Open Source Intelligence (OSINT). These include websites like Facebook, 
LinkedIn, Google search, and many others.

Using the steps described above to find out where an email originated from can help identify 
these targeted attacks.

## Poor spelling and grammar

When an organization sends out an email to customers on behalf of the company, the emails tend 
to be reviewed heavily for spelling and grammar mistakes. If you receive messages with poor 
grammar and spelling errors, more than likely it did not come from the company in question.

## Never send personal information through an email

If you receive unsolicited email asking for personal information, do not provide any 
information. Instead, visit the company's website and find the customer support email to 
contact the company, or give them a call.

## Be cautious with attachments

If you receive an email with an attachment that is unexpected, do not open it. Contact the 
sender to verify the email and attachment before opening it. When contacting the sender, do not 
reply to the original email otherwise you may be responding to an attacker.

[email-headers]: https://mxtoolbox.com/Public/Content/EmailHeaders/
[header-analyzer]: https://mxtoolbox.com/EmailHeaders.aspx