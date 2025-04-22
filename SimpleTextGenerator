using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using TMPro;

[System.Serializable]
public class TextPart
{
    public string text;
}


[System.Serializable]
public class TextContent
{
    public string role;
    public TextPart[] parts;
}

[System.Serializable]
public class TextCandidate
{
    public TextContent content;
}

[System.Serializable]
public class TextResponse
{
    public TextCandidate[] candidates;
}

[System.Serializable]
public class ChatRequest
{
    public TextContent[] contents;
}

public class SimpleTextGenerator : MonoBehaviour
{
    [Header("API Configuration")]
    private string apiKey = ""; // Add your API key here
    //Hide your API key (there are multiple ways), use this method only for testing purposes.
    private string apiEndpoint = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent";

    [Header("UI Elements")]
    public string inputField;
    public TMP_Text uiText;

    private TextContent[] chatHistory;

    void Start()
    {
        chatHistory = new TextContent[] { };
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.X)) //Press "x" to test 
        {
            ClearChatHistory();
            SendPrompt();
        }
    }
    public void SendPrompt()
    {
        string userPrompt = inputField;
        StartCoroutine(SendPromptRequest(userPrompt));
    }
    public void ClearChatHistory()
    {
        chatHistory = new TextContent[] { }; // Change with empty array
        Debug.Log("Chat history cleared.");
    }
    private IEnumerator SendPromptRequest(string promptText)
    {
        string url = $"{apiEndpoint}?key={apiKey}";
        string jsonData = "{\"contents\": [{\"parts\": [{\"text\": \"" + promptText + "\"}]}]}";
        byte[] jsonToSend = System.Text.Encoding.UTF8.GetBytes(jsonData);

        using (UnityWebRequest www = new UnityWebRequest(url, "POST"))
        {
            www.uploadHandler = new UploadHandlerRaw(jsonToSend);
            www.downloadHandler = new DownloadHandlerBuffer();
            www.SetRequestHeader("Content-Type", "application/json");

            yield return www.SendWebRequest();

            if (www.result != UnityWebRequest.Result.Success)
            {
                Debug.LogError(www.error);
            }
            else
            {
                TextResponse response = JsonUtility.FromJson<TextResponse>(www.downloadHandler.text);
                if (response.candidates.Length > 0 && response.candidates[0].content.parts.Length > 0)
                {
                    string generatedText = response.candidates[0].content.parts[0].text;
                    uiText.text = generatedText; // Show on ui
                    Debug.Log(generatedText);
                }
                else
                {
                    Debug.Log("No text found.");
                }
            }
        }
    }
}
