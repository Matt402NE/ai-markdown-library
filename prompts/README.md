# Prompts




### How to Use Prompt Files

- Write the prompt starting with a sample, from scratch, or with the help of Copilot.
- Place it in the `.github/prompts` folder in your workspace with the filename format `[promptname].prompt.md`, for example CreateAnalyzer.prompt.md.
- In VS Code, type `/[promptName]` in the chat to use it, and in VS, type `#[promptName]` in the chat to use it. For example, to create an analyzer that checks for weak hashing techniques using my CreateAnalyzer prompt, I would type “/CreateAnalyzer detect usage of MD5.Create(), SHA1.Create(), RijndaelManaged with insecure modes, new HMACSHA1(), or TripleDES and suggest using SHA256 or RandomNumberGenerator and point to library wrappers”.


## Online Resources

-  [anthropics skills](https://github.com/anthropics/skills)
- [github/awesome-copilot](https://github.com/github/awesome-copilot)
- [SebastienDegodez/copilot-instructions](https://github.com/SebastienDegodez/copilot-instructions)
