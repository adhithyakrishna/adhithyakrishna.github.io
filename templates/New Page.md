---
title: <%* let title = await tp.system.prompt("Enter the title of the page", "") %><% title%>
date: <% tp.file.creation_date("YYYY-MM-DDTHH:mm:ssZ") %>
draft: false
description: <%* let description = await tp.system.prompt("Enter the description of the section", "") %><% description%>
meta_image: <% tp.file.folder() %>/
image: <% tp.file.folder() %>/
weight: 1
---
