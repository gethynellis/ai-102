Certainly! Here's the complete solution based on the starter Python file and the instructions provided in the lab:

### Final Solution: Python Code for Azure AI Translator

```python
from dotenv import load_dotenv
import os
import requests, json
from azure.core.credentials import AzureKeyCredential
from azure.ai.translation.text import TextTranslationClient
from azure.ai.translation.text.models import InputTextItem

# Import namespaces added above as per instructions

def main():
    global translator_endpoint
    global cog_key
    global cog_region

    try:
        # Get Configuration Settings
        load_dotenv()
        cog_key = os.getenv('COG_SERVICE_KEY')
        cog_region = os.getenv('COG_SERVICE_REGION')
        translator_endpoint = 'https://api.cognitive.microsofttranslator.com'

        # Create client using endpoint and key
        credential = AzureKeyCredential(cog_key)
        client = TextTranslationClient(credential=credential, endpoint=translator_endpoint)

        # Choose target language
        languagesResponse = client.get_languages(scope="translation")
        print("{} languages supported.".format(len(languagesResponse.translation)))
        print("(See https://learn.microsoft.com/azure/ai-services/translator/language-support#translation)")
        print("Enter a target language code for translation (for example, 'en'):")
        
        targetLanguage = "xx"
        supportedLanguage = False
        while supportedLanguage == False:
            targetLanguage = input()
            if targetLanguage in languagesResponse.translation.keys():
                supportedLanguage = True
            else:
                print("{} is not a supported language.".format(targetLanguage))

        # Analyze each text file in the reviews folder
        reviews_folder = 'reviews'
        for file_name in os.listdir(reviews_folder):
            # Read the file contents
            print('\n-------------\n' + file_name)
            text = open(os.path.join(reviews_folder, file_name), encoding='utf8').read()
            print('\n' + text)

            # Detect the language
            language = GetLanguage(text, client)
            print('Language:', language)

            # Translate if not already English
            if language != 'en':
                translation = Translate(text, language, targetLanguage, client)
                print("\nTranslation:\n{}".format(translation))
                
    except Exception as ex:
        print(ex)

def GetLanguage(text, client):
    # Default language is English
    language = 'en'

    # Use the Azure AI Translator detect function
    input_text_elements = [InputTextItem(text=text)]
    detection_response = client.detect_language(content=input_text_elements)
    if detection_response:
        language = detection_response[0].detected_language.language

    # Return the detected language
    return language

def Translate(text, source_language, target_language, client):
    translation = ''

    # Use the Azure AI Translator translate function
    input_text_elements = [InputTextItem(text=text)]
    translation_response = client.translate(content=input_text_elements, to=[target_language])

    if translation_response:
        translated_text = translation_response[0].translations[0].text
        translation = translated_text

    # Return the translation
    return translation

if __name__ == "__main__":
    main()
```

### Explanation of the Solution:

1. **Configuration Settings**:
   - The `.env` file is loaded to fetch the `COG_SERVICE_KEY` and `COG_SERVICE_REGION`. These credentials are required for the Azure Translator service.

2. **Client Creation**:
   - The `TextTranslationClient` is created using the `AzureKeyCredential` and the Azure Translator API endpoint. This client is used to make requests to the Translator service.

3. **Language Selection**:
   - The program queries the list of supported languages for translation and prompts the user to choose a target language for translation. The language is verified against the supported languages before continuing.

4. **File Processing**:
   - The program loops through each text file in the `reviews` folder, reads its contents, and prints it to the console.

5. **Language Detection**:
   - The `detect_language` function from the Translator service is used to detect the language of the text. If the detected language is not English (`en`), the text will be translated.

6. **Translation**:
   - If the detected language is not English, the `Translate` function is called, which uses the Azure Translator's `translate` function to translate the text into the target language chosen earlier by the user. The translated text is then printed.

### How to Use the Code:
1. Make sure to create a `.env` file with the correct Azure Cognitive Services API key and region:
   ```env
   COG_SERVICE_KEY=your_cognitive_service_key
   COG_SERVICE_REGION=your_cognitive_service_region
   ```

2. Place this Python script (`translate.py`) in a folder that contains a `reviews` subfolder with text files for analysis.

3. Run the Python script from the terminal:
   ```bash
   pip install azure-ai-translation-text==1.0.0b1
   pip install python-dotenv
   python translate.py
   ```

The program will then ask for a target language code (e.g., 'en' for English), detect the language of each text file, and translate the text into the target language if necessary. It will print both the original and translated text in the console.
