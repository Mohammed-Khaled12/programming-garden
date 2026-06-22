# الدافع او المشكله:

كلنا عارفين ان ستيم و انت بتلعب لعبه عليه بيحسبلك الوقت اللي قضيته فيها بس المشكله ان مش كل الناس بتشتري العاب علي ستيم , الناس اللي بتلعب العاب Cracked هيتجهوا انهم ينزلوا لانشرز زي Playnite و دي مليانه Bloatware و بتستهلك موارد الجهاز 

# عايز تعمل ايه؟

انا فكرتي بناء Lightweight Desktop App لنظام Windows 11 
البرنامج عبارة عن Background Service بتشتغل في صمت جنب الساعة (System Tray) وتراقب الـ `.exe` الخاص بالألعاب عشان تحسب وتخزن وقت اللعب بدون ما تعمل أي Load على الـ CPU أو الـ RAM.
Monitoring running processes via Windows API
طبعا هيبقي فيه Features اكتر زي:
- **Smart AFK Detection (Idle Tracking):** Monitors mouse and keyboard inputs system-wide. If no activity is detected for a predefined threshold (e.g., 10 minutes), the app automatically pauses the active timer to prevent logging inflated playtime while away from the keyboard.

- **Auto-Discovery Prompt:** Instead of requiring manual `.exe` path configurations, the background service intelligently monitors running processes. Upon detecting a process with a significant spike in GPU utilization, it triggers an unobtrusive system notification asking: "Is this a new game you want to track?".

- **Performance Impact Logging:** Integrates with hardware monitoring APIs to log system resource utilization (such as VRAM allocation and GPU usage) during gameplay. This ensures the game runs optimally without frame stuttering, fully utilizing high-end hardware capabilities.

# المتطلبات:
C# .NET
JSON OR SQLite
Be Good at Operating Systems (Kernel,Processes,Threads and Memory Management)