---
title: <%* let title = await tp.system.prompt("Enter the title of the section", "") %><% title%>
description: <%* let description = await tp.system.prompt("Enter the description of the section", "") %><% description%>
date: <% tp.file.creation_date("YYYY-MM-DDTHH:mm:ssZ") %>
draft : false
collapsible : true
weight : 1
---
