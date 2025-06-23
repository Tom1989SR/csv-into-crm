import csv
import requests

# --- CONFIGURATION SECTION ---
CSV_FILE = 'new_leads.csv'
CRM_API_URL = 'https://your-crm-domain.com/api/leads'
API_KEY = 'your_api_key_here'

# --- LEAD FIELD MAPPING (CSV column names to CRM field names) ---
FIELD_MAPPING = {
    'First Name': 'first_name',
    'Last Name': 'last_name',
    'Email': 'email',
    'Phone': 'phone',
    'Company': 'company',
    'Source': 'lead_source'
}

def format_lead(row):
    return {crm_field: row[csv_field] for csv_field, crm_field in FIELD_MAPPING.items() if csv_field in row}

def import_lead_to_crm(lead):
    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'Content-Type': 'application/json'
    }
    response = requests.post(CRM_API_URL, json=lead, headers=headers)
    if response.status_code == 201:
        print(f"Lead {lead.get('email', '')} imported successfully.")
    else:
        print(f"Failed to import lead {lead.get('email', '')}: {response.status_code} {response.text}")

def main():
    with open(CSV_FILE, newline='', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile)
        for row in reader:
            formatted_lead = format_lead(row)
            import_lead_to_crm(formatted_lead)

if __name__ == '__main__':
    main()
