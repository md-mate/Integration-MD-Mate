# WebSocket Integration Documentation

Hei eng kleng Beschreiwung dovun wéi mir ganz gaeren mat ierch integréieren géifen.
Idealerweis géifen d Dokteren iwwert deen Websocket eng real-time notificatioun mam Rapport vum *AKTUELLEN PATIENT* am beschten direkt an déi richteg Felder.


## WebSocket Endpoint

wss://upload-with-html.onrender.com/reports/ws  --->temporary address doctors shoudl be able to change
THIS WILL SWITCH OFTEN

## Authentication

After establishing the WebSocket connection, you must send an authentication message within 10 seconds.
The authentication message should be a JSON object containing the doctor's API key:
{
"doctor_key": "api-key"
}

## Connection Lifecycle

Connect to the WebSocket endpoint
Send authentication message within 10 seconds
Maintain connection with ping/pong messages (optional)
Handle incoming notifications
Handle disconnection scenarios

### Ping/Pong Heartbeat

To keep the connection alive, you can send a ping message:
- Send: "ping"
- Receive: "pong"
Ideally every 30 seconds

## Notifications
### Report Ready Notification

When a report is ready, you'll receive a JSON message with the following structure:
{
"type": "report_ready", (STRING)
"url": "signed-url-to-download-docx", (STRING ---> signed URL to download the docx file)
"html_content": "html-version-of-report", (STRING ---> html version of the report)
"rtf_report": "rtf-version-of-report", (STRING ---> rich text format version of the report)
"plaintext_report": "plain-text-version", (STRING ---> plain text version of the report)
"json_report": ----> NESTED JSON OBJECT with access to all the data in the report
{
"history": "patient-history",
"findings": "examination-findings",
"diagnosis": "diagnosis",
"procedure": "procedures-performed",
"recommendation": "recommendations"
}

}

### Error Notifications

If an error occurs, you'll receive a JSON message with the following structure:

{
"type": "error",
"message": "error-description",
"code": 4000
}

## Connection Codes

1000: Normal closure
1008: Policy violation (usually authentication failure)
4000: Generic error
## Best Practices

Authentication: Store and handle the API key securely.
Reconnection: Implement an exponential backoff strategy for reconnection attempts.
Error Handling: Always handle error messages appropriately.
Connection Status: Monitor the connection status and implement appropriate UI feedback.
Message Parsing: Validate all incoming messages before processing.
## Rate Limits and Timeouts

Authentication timeout: 10 seconds
Connection limit: One active connection per user ID
Recommended ping interval: 30 seconds
