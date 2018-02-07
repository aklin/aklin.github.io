---
layout: page
title: Curriculum Vitae
slug: cv
permalink: /cv/
---

<style>
.post-title{margin-bottom: 2em;}
h2,h3,h4,h5,p{margin-left: 1em;}
h2 ~ h3, h4, h5, p {margin-left: 2em;}
</style>

Experienced in **Java** (and **Maven**) as well as **Oracle SQL**. Also familiar with **C, PHP** and **MySQL**.
Most comfortable as a backend developer, but more than capable of writing frontend code in **CSS (SASS/LESS)**,
**HTML5** and **Javascript**. I like to manage my code with **Git**.


# Experience

## Dotmatics LTD
**Full Stack Developer**, _December 2015 --- Present_


**Tech stack**: Java, JSP, Oracle SQL, (HTML / CSS / JS), jQuery, git-svn, TDD, Continuous Integration

### Current Projects

During downtime I'm working towards Dockerizing the [Browser][link-bro] suite. The main challenge I'm facing is packaging
[Pinpoint][link-pp] (our chemical cartridge) on top of Oracle DB and turning manual installation steps into
dependable executable scripts.

### Achievments

* Designed & Implemented £40K RESTful API before my 6th month
* Integrated [MarvinJS][marvinjs] with the [Register][link-reg] suite
* Completely rewrote the _Datasources Editor_ front-end, a core [Browser][link-bro] component
* Refactored Inventory Positional Indexing system

### Responsibilies

In the past I've worked on both [Register][link-reg] and the core [Browser][link-bro] modules, but these days
my work is focused mostly on [Inventory][link-inv]. On the design side of the equation I provide the technical
insight to assist the development of the product's business direction.



## EuroBalt
**Systems Administrator**, _May 2014 --- December 2015_


As the sole IT staff in this e-shop management startup I was responsible for maintaining our servers,
and implementing new solutions to accommodate our needs.

* Researched & Implemented Desktop-as-a-Service solution using X2Go
* Setup and maintenance of corporate File Sharing server (ownCloud)
* Developed assorted shell scripts to handle server setup, backups, etc.
* Automated routine manual tasks with Selenium (Java)

Νon-IT focused responsibilities included:

* Bookkeeping multiple server providers
* Significantly reduced costs for certain resources (up to 60%)


# Currently Learning

 Spring, Hibernate and Docker.


# Education

## University of Bristol

Postgraduate Certificate --- _Internet Technologies & Security_

_2012 --- 2014_

Learned to program a Beowulf cluster with OpenMP and OpenCL. Implemented RSA, ElGamal in C (using GMP).


## University of Hertfordshire

BSc (Hons), 1st Class --- _Computer Science_

_2009 --- 2012_

Foundations of computing and software development. My dissertation topic was the design and implementation
of a secure chat system, comprised of a server (C, MySQL), client (Java, Swing) and a sign-up website (PHP).


# Fun

## _(Gaming Community)_

**Chief Engineer - Community Manager**, _January 2017 --- Present_

Entrusted with the precious few hours<sup>1</sup> of fun of about 30 active mature-aged members. As the
founder my most important contribution was establishing a culture of openness and fairness, 
from the handling of our financials to the (rare but inevitable) enforcement of rules.

* Ensure our Docker-based forum and Linux game server are always up and running
* Maintain AWS-hosted utilities (NodeJS, AWS Lambda)
* Ensure a bus factor of > 1 in administrative roles at all times
* Maintain KB and train admins on procedures, including backup recovery

Soft skills include:

* Working with _Recruitment Officers_ and _Instructors_ to establish a high standard of training
* Directing talent to the appropriate resources (and then getting out of their way!)
* Interfacing with foreign teams for joint events
* Occasional conflict resolution


{% if site.cv.calendly %}

---

# Contact

<br />


{% include calendly.html %}
{% endif %}

<sup><sup>1</sup>That's 510 man-hours per month on average (1.7 hours per event, 3 times a week)</sup>


[link-pp]: https://www.dotmatics.com/products/pinpoint
[link-inv]: https://www.dotmatics.com/products/inventory
[link-bro]: https://www.dotmatics.com/products/browser
[link-reg]: https://www.dotmatics.com/products/register
[marvinjs]: https://www.chemaxon.com/products/marvin/marvin-js/