
### Final Solution: Python Code for the Text-Analysis Lab

```python
from dotenv import load_dotenv
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.textanalytics import TextAnalyticsClient

# Import namespaces are added above as per instructions

def main():
    try:
        # Get Configuration Settings
        load_dotenv()
        ai_endpoint = os.getenv('AI_SERVICE_ENDPOINT')
        ai_key = os.getenv('AI_SERVICE_KEY')

        # Create client using endpoint and key
        credential = AzureKeyCredential(ai_key)
        ai_client = TextAnalyticsClient(endpoint=ai_endpoint, credential=credential)

        # Analyze each text file in the reviews folder
        reviews_folder = 'reviews'
        for file_name in os.listdir(reviews_folder):
            # Read the file contents
            print('\n-------------\n' + file_name)
            text = open(os.path.join(reviews_folder, file_name), encoding='utf8').read()
            print('\n' + text)

            # Get language
            detected_language = ai_client.detect_language(documents=[text])[0]
            print('\nLanguage: {}'.format(detected_language.primary_language.name))

            # Get sentiment
            sentiment_analysis = ai_client.analyze_sentiment(documents=[text])[0]
            print("\nSentiment: {}".format(sentiment_analysis.sentiment))

            # Get key phrases
            phrases = ai_client.extract_key_phrases(documents=[text])[0].key_phrases
            if len(phrases) > 0:
                print("\nKey Phrases:")
                for phrase in phrases:
                    print('\t{}'.format(phrase))

            # Get entities
            entities = ai_client.recognize_entities(documents=[text])[0].entities
            if len(entities) > 0:
                print("\nEntities")
                for entity in entities:
                    print('\t{} ({})'.format(entity.text, entity.category))

            # Get linked entities
            linked_entities = ai_client.recognize_linked_entities(documents=[text])[0].entities
            if len(linked_entities) > 0:
                print("\nLinks")
                for linked_entity in linked_entities:
                    print('\t{} ({})'.format(linked_entity.name, linked_entity.url))

    except Exception as ex:
        print(ex)


if __name__ == "__main__":
    main()
```

### Explanation of the Solution:
1. **Configuration Settings**:
   - The `dotenv` library is used to load environment variables from a `.env` file to retrieve the `AI_SERVICE_ENDPOINT` and `AI_SERVICE_KEY`.

2. **Create the Client**:
   - A `TextAnalyticsClient` is created using the `AzureKeyCredential` and the `AI_SERVICE_ENDPOINT`.

3. **File Processing**:
   - The program loops through all text files in the `reviews` folder.
   - Each file is read, and its contents are processed through various Text Analytics API calls.

4. **Language Detection**:
   - The `detect_language` method is called to detect the language of the text, and the detected language is printed.

5. **Sentiment Analysis**:
   - The `analyze_sentiment` method analyzes the sentiment of the text, which is then printed.

6. **Key Phrase Extraction**:
   - The `extract_key_phrases` method identifies key phrases in the text, and they are printed.

7. **Entity Recognition**:
   - The `recognize_entities` method is used to detect entities within the text (like people, places, etc.), which are printed with their category.

8. **Linked Entity Recognition**:
   - The `recognize_linked_entities` method identifies entities linked to external sources (such as Wikipedia), and the linked name and URL are printed.

### How to Use the Code:
- Ensure that the `.env` file contains valid Azure AI service credentials for `AI_SERVICE_ENDPOINT` and `AI_SERVICE_KEY`.
- Place the Python script (`text-analysis.py`) in a folder that contains a `reviews` subfolder with text files for analysis.
- Run the Python script from the terminal after installing the required packages:
   ```bash
   pip install azure-ai-textanalytics==5.3.0
   pip install python-dotenv
   python text-analysis.py
   ```

This should now fully implement the lab's functionality for analyzing text documents using Azure's Text Analytics API.
