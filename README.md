# Medical-document-processor-workflow-n8n
n8n workflow for processing medical documents with AI classification
# Medical Document Processing Workflow for n8n

An automated workflow that processes medical documents (PDFs, images) using Google Gemini AI for text extraction and intelligent classification.

## Features

- 📄 Multi-file upload support (up to 5 documents)
- 🤖 AI-powered OCR and text extraction using Google Gemini
- 🏥 Intelligent document classification for medical practices
- 📧 Automated email routing with aggregated results
- 📎 All documents attached to single summary email

## Workflow Overview

1. **Form Submission** - Users upload up to 5 medical documents
2. **Text Extraction** - Gemini AI extracts text from PDFs/images
3. **Classification** - AI categorizes documents and extracts patient info
4. **Aggregation** - All results combined into single email
5. **Delivery** - Summary email sent with all documents attached

## Document Categories

- Referral (new/existing patient)
- Imaging request or report
- Surgical booking
- Billing/administrative
- Records request
- Other

## Requirements

- n8n instance (v1.114.4 or later)
- Google Gemini API key
- Gmail account with OAuth2 configured

## Installation

1. Import the workflow JSON into your n8n instance
2. Configure credentials:
   - Google Gemini API (get key from https://ai.google.dev/)
   - Gmail OAuth2
3. Update the recipient email address in the Gmail node
4. Activate the workflow

## Setup Instructions

### 1. Get Google Gemini API Key

1. Visit https://ai.google.dev/
2. Create a new API key
3. Add to n8n: Credentials → Add → Google PaLM API

### 2. Configure Gmail

1. In n8n: Credentials → Add → Gmail OAuth2
2. Follow OAuth setup flow
3. Grant necessary permissions

### 3. Import Workflow

1. Download `medical device processor.json` from this repo
2. In n8n: Click **Import from File**
3. Select the downloaded JSON
4. Configure credentials in each node

## Usage

1. Access the form URL (shown in the Form Trigger node)
2. Upload 1-5 medical documents
3. Fill in document type and notes
4. Submit form
5. Receive aggregated email with all documents and AI analysis

## Workflow Structure
```
Form Trigger
    ↓
Split Files for Processing
    ↓                    ↓
    |                    └────────────────→ Merge (Input 2)
    ↓                                           ↑
Interpret the file                              |
    ↓                                           |
Make it ready for Gemini                        |
    ↓                                           |
HTTP Request (Gemini API)                       |
    ↓                                           |
Parse Response ─────────────────────→ Merge (Input 1)
                                               ↓
                              Build Email with Attachments
                                               ↓
                                         Send Email (Gmail)
```

## Configuration

### Maximum Files

By default, the workflow supports up to 5 files. To change this:

1. Edit "Build Email with Attachments" node
2. Change `MAX_FILES` constant
3. Add/remove attachment fields in Gmail node accordingly

### Email Template

Customize the email summary in the "Build Email with Attachments" node by modifying the `summaryText` variable.

### Document Classification

Adjust classification logic in "Make it ready for Gemini" node by editing the prompt.

## Technical Details

### Binary Data Handling

The workflow uses a special technique to preserve binary data through HTTP Request nodes:
- Files are stored as base64 in JSON (`stored_binary` field)
- Merge node combines classification results with original binaries
- Placeholder PDFs pad empty attachment slots

### AI Models Used

- **Gemini 2.5 Flash** for text extraction (fast, cost-effective)
- **Gemini 2.5 Flash** for classification (JSON response mode)

## Troubleshooting

### Binary data not attaching

- Check that "Split Files for Processing" creates `stored_binary` field
- Verify Merge node has both inputs connected
- Ensure "Build Email with Attachments" shows 5 binary keys in debug output

### Gmail authentication errors

- Re-authenticate Gmail OAuth2 credentials
- Check OAuth consent screen settings
- Verify Gmail API is enabled in Google Cloud Console

### Gemini API errors

- Verify API key is valid
- Check API quotas and limits
- Ensure you're using the correct model name

## Cost Considerations

- Gemini 2.5 Flash: ~$0.00001875 per document (estimate)
- Gmail: Free (standard limits apply)
- n8n: Self-hosted (free) or Cloud (paid plans)

## Security Notes

- **IMPORTANT**: Never commit API keys to the repository
- Use n8n's credential management system
- Consider encrypting sensitive data in transit
- Implement proper access controls on the form URL

## License

MIT License - feel free to modify and distribute

## Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request with clear description

## Support

For issues or questions:
- Open a GitHub issue
- Check n8n community forum
- Review n8n documentation

## Changelog

### v1.0.0 (2024-12-01)
- Initial release
- Multi-file support (up to 5 files)
- Gemini AI integration
- Email aggregation

## Acknowledgments

- Built with [n8n](https://n8n.io/)
- Powered by [Google Gemini AI](https://ai.google.dev/)
- Inspired by real medical practice workflows

## Disclaimer

This workflow is for educational/demonstration purposes. Ensure HIPAA compliance and proper security measures before using with real patient data.
