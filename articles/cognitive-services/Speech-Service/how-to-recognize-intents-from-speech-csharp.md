---
title: Recognize intents from speech by using the Speech SDK for C#
titleSuffix: "Microsoft Cognitive Services"
description: |
  Learn how to recognize intents from speech from a file or a microphone by using the Speech SDK for C#.
services: cognitive-services
author: wolfma61

ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
---

# Recognize intents from speech by using the Speech SDK for C#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone.md)]

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!INCLUDE [Introduction - using microphone and language](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone-language.md)]

[!code-csharp[Intent recognition by using a microphone in a specified language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-continuous.md)]

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Look for the code that's used in this article in the samples/csharp/sharedcontent/console folder.

## Next steps

- [How to recognize speech](how-to-recognize-speech-csharp.md)
- [How to translate speech](how-to-translate-speech-csharp.md)
