# 🧾 Intelligent Invoice Processing with UiPath Action Center

An enterprise-grade RPA project built with UiPath Studio, demonstrating end-to-end document processing using Artificial Intelligence (Document Understanding) and Human-in-the-Loop validation (Action Center).

## 🚀 Architecture Overview

This project is divided into a Dispatcher and a Performer, utilizing the robust **Robotic Enterprise Framework (REFramework)** to ensure scalability, error handling, and transactional processing.

### 1. Dispatcher
- Scans a local directory for incoming Invoice PDFs.
- Adds each document securely to an Orchestrator Queue.

### 2. Performer (REFramework + Document Understanding)
- **Initialization:** Connects to Orchestrator, loads configurations, and fetches the Queue Item.
- **Digitization & Classification:** Uses UiPath Document OCR to digitize the PDF.
- **AI Data Extraction:** Utilizes the `Machine Learning Extractor` (Invoices model) to intelligently extract unstructured data:
  - Invoice Number
  - Vendor VAT Number (CNPJ)
  - Total Amount
- **Human-in-the-Loop (Action Center):** Evaluates the extraction confidence against a dynamic threshold (e.g., 80%). If confidence is low, the robot creates a Document Validation Action in Orchestrator and **Suspends** execution (releasing the machine license).
- **Validation:** A human operator reviews and corrects the fields via the Action Center web interface.
- **Resume & Export:** Upon submission by the human, the robot awakens, retrieves the validated data, and constructs a JSON payload.
- **API Integration:** Dispatches the final, validated JSON data to a downstream system using an HTTP POST request (via Webhook).

## 🛠️ Technologies Used
- UiPath Studio (Windows / VB)
- Robotic Enterprise Framework (REFramework)
- UiPath Document Understanding (ML Extractor)
- UiPath Action Center
- UiPath WebAPI (HTTP Requests)
- JSON Data Structuring

## 💡 Key Learnings
- Managing complex Variable Scopes during workflow suspension and resumption.
- Overriding Data Extraction mechanisms to gracefully handle both automated and human-validated paths using inline IF conditions.
- Integrating external APIs (Webhooks) natively within the REFramework state machine.

## ⚙️ How to Run
1. Update `Config.xlsx` with your Orchestrator Queue name, Storage Bucket name, and Document Understanding API Key.
2. Run `Dispatcher.xaml` to populate the queue with sample PDFs.
3. Run `Main.xaml` to start the Performer.
4. Access UiPath Orchestrator > Action Center to validate the suspended tasks.
