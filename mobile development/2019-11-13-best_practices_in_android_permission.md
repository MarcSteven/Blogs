#1: Only use the permissions necessary for your app to work. Depending on how you are using the permissions, there may be another way to do what you need (system intents, identifiers, backgrounding for phone calls) without relying on access to sensitive information.

#2: Pay attention to permissions required by libraries. When you include a library, you also inherit its permission requirements. You should be aware of what you're including, the permissions they require, and what those permissions are used for.

#3: Be transparent. When you make a permissions request, be clear about what you’re accessing, and why, so users can make informed decisions. Make this information available alongside the permission request including install, runtime, or update permission dialogues.

#4: Make system accesses explicit. Providing continuous indications when you access sensitive capabilities (for example, the camera or microphone) makes it clear to users when you’re collecting data and avoids the perception that you're collecting data surreptitiously.
