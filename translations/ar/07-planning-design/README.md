<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "e4e06d3b5d6207459a019c05fee5eb4b",
  "translation_date": "2025-07-12T10:36:04+00:00",
  "source_file": "07-planning-design/README.md",
  "language_code": "ar"
}
-->
للحصول على نظرة سريعة.

يوضح المقتطف البرمجي التالي بلغة بايثون وكيل تخطيط بسيط يقوم بتقسيم الهدف إلى مهام فرعية ويولد خطة منظمة:

```python
from pydantic import BaseModel
from enum import Enum
from typing import List, Optional, Union
import json
import os
from typing import Optional
from pprint import pprint
from autogen_core.models import UserMessage, SystemMessage, AssistantMessage
from autogen_ext.models.azure import AzureAIChatCompletionClient
from azure.core.credentials import AzureKeyCredential

class AgentEnum(str, Enum):
    FlightBooking = "flight_booking"
    HotelBooking = "hotel_booking"
    CarRental = "car_rental"
    ActivitiesBooking = "activities_booking"
    DestinationInfo = "destination_info"
    DefaultAgent = "default_agent"
    GroupChatManager = "group_chat_manager"

# Travel SubTask Model
class TravelSubTask(BaseModel):
    task_details: str
    assigned_agent: AgentEnum  # we want to assign the task to the agent

class TravelPlan(BaseModel):
    main_task: str
    subtasks: List[TravelSubTask]
    is_greeting: bool

client = AzureAIChatCompletionClient(
    model="gpt-4o-mini",
    endpoint="https://models.inference.ai.azure.com",
    # To authenticate with the model you will need to generate a personal access token (PAT) in your GitHub settings.
    # Create your PAT token by following instructions here: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens
    credential=AzureKeyCredential(os.environ["GITHUB_TOKEN"]),
    model_info={
        "json_output": False,
        "function_calling": True,
        "vision": True,
        "family": "unknown",
    },
)

# Define the user message
messages = [
    SystemMessage(content="""You are an planner agent.
    Your job is to decide which agents to run based on the user's request.
                      Provide your response in JSON format with the following structure:
{'main_task': 'Plan a family trip from Singapore to Melbourne.',
 'subtasks': [{'assigned_agent': 'flight_booking',
               'task_details': 'Book round-trip flights from Singapore to '
                               'Melbourne.'}
    Below are the available agents specialised in different tasks:
    - FlightBooking: For booking flights and providing flight information
    - HotelBooking: For booking hotels and providing hotel information
    - CarRental: For booking cars and providing car rental information
    - ActivitiesBooking: For booking activities and providing activity information
    - DestinationInfo: For providing information about destinations
    - DefaultAgent: For handling general requests""", source="system"),
    UserMessage(
        content="Create a travel plan for a family of 2 kids from Singapore to Melboune", source="user"),
]

response = await client.create(messages=messages, extra_create_args={"response_format": 'json_object'})

response_content: Optional[str] = response.content if isinstance(
    response.content, str) else None
if response_content is None:
    raise ValueError("Response content is not a valid JSON string" )

pprint(json.loads(response_content))

# # Ensure the response content is a valid JSON string before loading it
# response_content: Optional[str] = response.content if isinstance(
#     response.content, str) else None
# if response_content is None:
#     raise ValueError("Response content is not a valid JSON string")

# # Print the response content after loading it as JSON
# pprint(json.loads(response_content))

# Validate the response content with the MathReasoning model
# TravelPlan.model_validate(json.loads(response_content))
```

### وكيل التخطيط مع تنسيق متعدد الوكلاء

في هذا المثال، يستقبل وكيل التوجيه الدلالي طلب المستخدم (مثل: "أحتاج خطة فندق لرحلتي.").

يقوم المخطط بعد ذلك بـ:

* استلام خطة الفندق: يأخذ المخطط رسالة المستخدم وبناءً على موجه النظام (بما في ذلك تفاصيل الوكلاء المتاحين)، يولد خطة سفر منظمة.
* سرد الوكلاء وأدواتهم: يحتفظ سجل الوكلاء بقائمة من الوكلاء (مثل وكيل الطيران، الفندق، تأجير السيارات، والأنشطة) مع الوظائف أو الأدوات التي يقدمونها.
* توجيه الخطة إلى الوكلاء المعنيين: اعتمادًا على عدد المهام الفرعية، إما يرسل المخطط الرسالة مباشرة إلى وكيل مخصص (للسيناريوهات ذات المهمة الواحدة) أو ينسق عبر مدير دردشة جماعية للتعاون بين عدة وكلاء.
* تلخيص النتيجة: أخيرًا، يلخص المخطط الخطة المولدة لتوضيحها.
يوضح المثال البرمجي التالي بلغة بايثون هذه الخطوات:

```python

from pydantic import BaseModel

from enum import Enum
from typing import List, Optional, Union

class AgentEnum(str, Enum):
    FlightBooking = "flight_booking"
    HotelBooking = "hotel_booking"
    CarRental = "car_rental"
    ActivitiesBooking = "activities_booking"
    DestinationInfo = "destination_info"
    DefaultAgent = "default_agent"
    GroupChatManager = "group_chat_manager"

# Travel SubTask Model

class TravelSubTask(BaseModel):
    task_details: str
    assigned_agent: AgentEnum # we want to assign the task to the agent

class TravelPlan(BaseModel):
    main_task: str
    subtasks: List[TravelSubTask]
    is_greeting: bool
import json
import os
from typing import Optional

from autogen_core.models import UserMessage, SystemMessage, AssistantMessage
from autogen_ext.models.openai import AzureOpenAIChatCompletionClient

# Create the client with type-checked environment variables

client = AzureOpenAIChatCompletionClient(
    azure_deployment=os.getenv("AZURE_OPENAI_DEPLOYMENT_NAME"),
    model=os.getenv("AZURE_OPENAI_DEPLOYMENT_NAME"),
    api_version=os.getenv("AZURE_OPENAI_API_VERSION"),
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),
)

from pprint import pprint

# Define the user message

messages = [
    SystemMessage(content="""You are an planner agent.
    Your job is to decide which agents to run based on the user's request.
    Below are the available agents specialized in different tasks:
    - FlightBooking: For booking flights and providing flight information
    - HotelBooking: For booking hotels and providing hotel information
    - CarRental: For booking cars and providing car rental information
    - ActivitiesBooking: For booking activities and providing activity information
    - DestinationInfo: For providing information about destinations
    - DefaultAgent: For handling general requests""", source="system"),
    UserMessage(content="Create a travel plan for a family of 2 kids from Singapore to Melbourne", source="user"),
]

response = await client.create(messages=messages, extra_create_args={"response_format": TravelPlan})

# Ensure the response content is a valid JSON string before loading it

response_content: Optional[str] = response.content if isinstance(response.content, str) else None
if response_content is None:
    raise ValueError("Response content is not a valid JSON string")

# Print the response content after loading it as JSON

pprint(json.loads(response_content))
```

ما يلي هو ناتج الكود السابق ويمكنك بعد ذلك استخدام هذا الناتج المنظم لتوجيهه إلى `assigned_agent` وتلخيص خطة السفر للمستخدم النهائي.

```json
{
    "is_greeting": "False",
    "main_task": "Plan a family trip from Singapore to Melbourne.",
    "subtasks": [
        {
            "assigned_agent": "flight_booking",
            "task_details": "Book round-trip flights from Singapore to Melbourne."
        },
        {
            "assigned_agent": "hotel_booking",
            "task_details": "Find family-friendly hotels in Melbourne."
        },
        {
            "assigned_agent": "car_rental",
            "task_details": "Arrange a car rental suitable for a family of four in Melbourne."
        },
        {
            "assigned_agent": "activities_booking",
            "task_details": "List family-friendly activities in Melbourne."
        },
        {
            "assigned_agent": "destination_info",
            "task_details": "Provide information about Melbourne as a travel destination."
        }
    ]
}
```

يتوفر دفتر ملاحظات يحتوي على المثال البرمجي السابق [هنا](../../../07-planning-design/07-autogen.ipynb).

### التخطيط التكراري

تتطلب بعض المهام تكرارًا أو إعادة تخطيط، حيث تؤثر نتيجة مهمة فرعية على المهمة التالية. على سبيل المثال، إذا اكتشف الوكيل تنسيق بيانات غير متوقع أثناء حجز الرحلات، فقد يحتاج إلى تعديل استراتيجيته قبل الانتقال إلى حجز الفنادق.

بالإضافة إلى ذلك، يمكن أن يؤدي ملاحظات المستخدم (مثل قرار بشري بتفضيل رحلة أبكر) إلى إعادة تخطيط جزئية. يضمن هذا النهج الديناميكي والتكراري أن الحل النهائي يتماشى مع القيود الواقعية وتفضيلات المستخدم المتغيرة.

مثال على الكود

```python
from autogen_core.models import UserMessage, SystemMessage, AssistantMessage
#.. same as previous code and pass on the user history, current plan
messages = [
    SystemMessage(content="""You are a planner agent to optimize the
    Your job is to decide which agents to run based on the user's request.
    Below are the available agents specialized in different tasks:
    - FlightBooking: For booking flights and providing flight information
    - HotelBooking: For booking hotels and providing hotel information
    - CarRental: For booking cars and providing car rental information
    - ActivitiesBooking: For booking activities and providing activity information
    - DestinationInfo: For providing information about destinations
    - DefaultAgent: For handling general requests""", source="system"),
    UserMessage(content="Create a travel plan for a family of 2 kids from Singapore to Melbourne", source="user"),
    AssistantMessage(content=f"Previous travel plan - {TravelPlan}", source="assistant")
]
# .. re-plan and send the tasks to respective agents
```

للتخطيط الأكثر شمولاً، تحقق من Magnetic One

لحل المهام المعقدة.

## الملخص

في هذا المقال، نظرنا في مثال على كيفية إنشاء مخطط يمكنه اختيار الوكلاء المتاحين بشكل ديناميكي. يقوم ناتج المخطط بتقسيم المهام وتعيين الوكلاء ليتم تنفيذها. يُفترض أن الوكلاء لديهم إمكانية الوصول إلى الوظائف/الأدوات المطلوبة لأداء المهمة. بالإضافة إلى الوكلاء، يمكنك تضمين أنماط أخرى مثل الانعكاس، الملخص، ودردشة التناوب لتخصيص أكثر.

## موارد إضافية

* AutoGen Magnetic One - نظام متعدد الوكلاء عام لحل المهام المعقدة وقد حقق نتائج مبهرة في عدة معايير تقييم تحدي الوكلاء. المرجع:

في هذا التنفيذ، يقوم المنسق بإنشاء خطة محددة لكل مهمة ويفوض هذه المهام إلى الوكلاء المتاحين. بالإضافة إلى التخطيط، يستخدم المنسق أيضًا آلية تتبع لمراقبة تقدم المهمة وإعادة التخطيط حسب الحاجة.

## الدرس السابق

[بناء وكلاء ذكاء اصطناعي موثوقين](../06-building-trustworthy-agents/README.md)

## الدرس التالي

[نمط تصميم متعدد الوكلاء](../08-multi-agent/README.md)

**إخلاء المسؤولية**:  
تمت ترجمة هذا المستند باستخدام خدمة الترجمة الآلية [Co-op Translator](https://github.com/Azure/co-op-translator). بينما نسعى لتحقيق الدقة، يرجى العلم أن الترجمات الآلية قد تحتوي على أخطاء أو عدم دقة. يجب اعتبار المستند الأصلي بلغته الأصلية المصدر الموثوق به. للمعلومات الهامة، يُنصح بالاعتماد على الترجمة البشرية المهنية. نحن غير مسؤولين عن أي سوء فهم أو تفسير ناتج عن استخدام هذه الترجمة.