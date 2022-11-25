---
title: "Most Common word"
description: "Solution for Leetcode 819"
date: 2021-01-13T15:01:46+00:00
draft: false
weight: 1
---

## Solution for leetcode 819
{{< tabs Golang Java >}}

{{< tab >}}
### Golang solution
```Go
func mostCommonWord(paragraph string, banned []string) string {

	var sb strings.Builder

	bannedMap := make(map[string]bool)

	for _, data := range banned {
		bannedMap[data] = true
	}

	wordCounts := make(map[string]int)
	var max string

	for _, ch := range paragraph {
		if string(ch) == " " || string(ch) == "," {
			if sb.Len() > 0 {
				max = addWordToMap(wordCounts, sb, max, bannedMap)
				sb.Reset()
			}
		} else {
			if unicode.IsLetter(ch) {
				sb.WriteString(string(ch))
			}
		}
	}

	if sb.Len() > 0 {
		max = addWordToMap(wordCounts, sb, max, bannedMap)
	}

	return max
}

func addWordToMap(wordCounts map[string]int, sb strings.Builder, max string, bannedMap map[string]bool) string {
	word := strings.ToLower(sb.String())
	wordCounts[word] += 1

	if !bannedMap[word] && wordCounts[word] > wordCounts[max] {
		max = word
	}

	return max
}
```

{{< boxmd >}}
References:
1. [Are Maps pass by reference in go ?](https://stackoverflow.com/questions/40680981/are-maps-passed-by-value-or-by-reference-in-go)
{{< /boxmd >}}


{{< /tab >}}

{{< tab >}}
### Java solution
```Java
class Solution {
    public String mostCommonWord(String paragraph, String[] banned) {
        Set<String> bannedSet = new HashSet<String>();
        
        for(String ban: banned) {
            bannedSet.add(ban);
        }
        
        StringBuffer sb = new StringBuffer();
        Map<String, Integer> dataMap = new HashMap<String, Integer>();
        String max = "";
        for(char ch : paragraph.toCharArray()) {
            if(ch == ' ' || ch == ',') {
                if(sb.length() > 0) {
                    max = addWordToMap(sb, bannedSet, dataMap, max);
                    sb.setLength(0);
                }
            }
            else {
                if(Character.isAlphabetic(ch)) {
                    sb.append(ch);
                }
            }
        }
        
        if(sb.length() > 0) {
            max = addWordToMap(sb, bannedSet, dataMap, max);
        }
        
        return max;
    }
    
    public String addWordToMap(StringBuffer sb, Set<String> bannedSet, Map<String, Integer> dataMap, String max) {
        String word = sb.toString().toLowerCase();
        if(bannedSet.contains(word))
            return max;
        dataMap.put(word, dataMap.getOrDefault(word, 0) + 1);
        if(dataMap.get(word) > dataMap.getOrDefault(max, 0)) {
            return word;
        }
        return max;
    }
}
```
{{< /tab >}}
{{< /tabs >}}

{{< box >}}
Time complexity : O(N + M)
Space complexity : O(N + M)
{{< /box >}}