---
title: Adhithyakrishna
description: A website to journal my learnings as a computer science engineer. 
date: 2020-01-26T04:15:05+09:00
draft: false
landing:
  height: 450
  image: images/section/joe.png
  title:
    - Adhithyakrishna
  text:
  titleColor:
  textColor:
  spaceBetweenTitleText: 8
  buttons:
    - link: documents/Adhithya_resume.pdf
      text: Download my Resume
      color: primary
footer:
  sections:
    - title: Navigation
      links:
        - title: Articles
          link: /articles
        - title: Blog
          link: /blog
    - title: Links
      links:
        - title: GitHub
          link: https://github.com/adhithyakrishna/
        - title: LinkedIn
          link: https://www.linkedin.com/in/adhithya-krishna/
    - title: Contact
      links:
        - title: Email
          link: mailto:akrishna.cse21@gmail.com
  contents: 
    align: left
    applySinglePageCss: false
    markdown:
      |
      ## Akrishna
      Copyright © 2021. All rights reserved.

sections:
  - bgcolor: primary
    type: card
    description: "I am currently working as a Software Engineer 2 at Nordstrom. I am a tech enthusiast with an infinite zeal to design and build scalable web applications. I obtained my masters degree in Computer and Information Sciences from University at Buffalo, New York."
    header: 
      title: About me
      hlcolor: "#5468d2"
      color: 
      fontSize: 32
      width: 290
    cards:
      - subtitle: Team Worker
        subtitlePosition: center
        image: images/section/team.png
        color: white
      - subtitle: Fast Learner
        subtitlePosition: center
        image: images/section/skills.png
        color: white
      - subtitle: Problem Solver
        subtitlePosition: center
        image: images/section/problemsolver.png
        color: white
  - bgcolor: primary
    type: normal
    header:
      title: Work experience
      hlcolor: "#5468d2"
      color: 
      fontSize: 32
      width: 340
    body:      
      cards:
        - company: Nordstrom, Inc.
          subtitlePosition: left
          position: Software Engineer 2
          timestamp : April 2021 - Present, Seattle, WA
          image: images/section/building.png
          imagePosition: left
          description :
            - point: Currently working on some interesting projects in the Inventory deployment and planning space.
            - point: I have hands on experience working with docker, kubernetes, AWS, Kafka, Gitlab-CI
        - company: Skava systems
          subtitlePosition: left
          position: Software Development Engineer
          timestamp : Jun 2016 - Jun 2019, Coimbatore, India 
          image: images/section/building.png
          imagePosition: left
          description:
            - point: Achieved a 12% increase in product search accuracy by engineering migration tool using the Spring framework to index around 2 million product data from the MySQL database into Apache Solr.
            - point: Planned and employed a test-driven development approach to develop restful microservices using Spring framework, MySQL database, OAuth2 for authentication and, and Apache Solr for caching.
            - point: Reduced page load time by 50% from 4 seconds to 2 seconds by designing and developing jQuery plugins.
        - company: Skava systems
          subtitlePosition: left
          position: Software Development Engineer Intern
          timestamp : Jan 2016 - Jun 2019, Coimbatore, India
          image: images/section/building.png
          imagePosition: left
          description:
            - point: Engineered a configuration based modular web scraper using Node.JS and Cheerio to dynamically retrieve product information of up to 5 websites and store them into a MongoDB database.
            - point: Created a complete wedding registry website using HTML5, CSS3, and jQuery.
  - bgcolor: primary
    type: skills
    header:
      title: Skills
      hlcolor: "#5468d2"
      color: 
      fontSize: 32
      width: 340
    body:
      skills:
        - name:  Java
          image: images/section/java.png
          imagePosition: left
          subtitle: Java
          description:
        - name: Golang
          image: images/section/go.png
          imagePosition: left
          description:
          subtitle: Golang
        - name: Git
          image: images/section/git.png
          imagePosition: left
          subtitle: Git
          description:
        - name: Javascript
          image: images/section/js.png
          imagePosition: left
          subtitle: Javascript
          description:
  - bgcolor: primary
    type: project
    header: 
      title: Projects
      hlcolor: "#5468d2"
      color: 
      fontSize: 32
      width: 290
    cards:
      - subtitle: BettingLite-Dapp
        subtitlePosition: center
        description: "A blockchain project aimed at providing a decentralized and anonymous betting platform."
        image: images/section/code.png
        color: white
        button: 
          name: Details
          link: https://github.com/adhithyakrishna/BlockChainProject
          size: large
          target: _blank
          color: 'white'
          bgcolor: '#5468d2'
      - subtitle: Saavn-nodejs  
        subtitlePosition: center
        description: "A node js wrapper for the jio saavn api, the motive of this project is to create a fullstack music streaming website."
        image: images/section/code.png
        color: white
        button: 
          name: Details
          link: https://github.com/adhithyakrishna/saavn-nodejs
          size: large
          target: _blank
          color: 'white'
          bgcolor: '#5468d2'
      - subtitle: Twitter Analyzer
        subtitlePosition: center
        description: "Project to analyze rhetoric of prominent public leader’s tweets based on multilingual search, sentiment analysis and topic analysis."
        image: images/section/code.png
        color: white
        button: 
          name: Details
          link: https://github.com/adhithyakrishna/Twitter-Analyzer
          size: large
          target: _blank
          color: 'white'
          bgcolor: '#5468d2'
      - subtitle: Distributed Key Value Storage
        subtitlePosition: center
        description: "This project focuses on implementing a distributed key-value storage system, performing data replictation, data partitioning and automatic failure recovery."
        image: images/section/code.png
        color: white
        button: 
          name: Details
          link: https://github.com/adhithyakrishna/Amazon-dynamo-based-key-value-storage
          size: large
          target: _blank
          color: 'white'
          bgcolor: '#5468d2'
      - subtitle: Distributed Messenger Application
        subtitlePosition: center
        description: "This project implements a distributed messenger application, providing Fifo and total ordering guarantees, supporting concurrent storage and retrieval of messages."
        image: images/section/code.png
        color: white
        button: 
          name: Details
          link: https://github.com/adhithyakrishna/Distributed-messenger-application
          size: large
          target: _blank
          color: 'white'
          bgcolor: '#5468d2'
---