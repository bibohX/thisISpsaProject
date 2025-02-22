import streamlit as st
import firebase_admin
from firebase_admin import credentials, firestore
from datetime import datetime

# Initialize Firebase
if not firebase_admin._apps:
    cred = credentials.Certificate("path/to/your/serviceAccountKey.json")  # Replace with your Firebase service account key
    firebase_admin.initialize_app(cred)

db = firestore.client()

# Streamlit App
st.title("Reference Number Generator")

# Function to generate reference numbers
def generate_reference_numbers(data):
    ref_numbers_collection = db.collection('reference_numbers')
    last_ref_doc = ref_numbers_collection.order_by('ref_number', direction=firestore.Query.DESCENDING).limit(1).stream()

    last_ref_number = "25PSO53-0000"  # Default starting reference number

    for doc in last_ref_doc:
        last_ref_number = doc.to_dict().get('ref_number', "25PSO53-0000")

    # Extract the last 4 digits and increment
    last_number = int(last_ref_number.split("-")[1])
    new_number = last_number + 1

    results = []

    for index, entry in enumerate(data):
        ref_number = f"25PSO53-{str(new_number + index).zfill(4)}"
        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

        # Add data to Firestore
        ref_numbers_collection.add({
            'timestamp': timestamp,
            'ref_number': ref_number,
            'name': entry['name'],
            'recipient': entry['recipient'],
            'subject': entry['subject'],
            'remarks': entry['remarks']
        })

        results.append(f"{ref_number}: {entry['name']}, {entry['subject']}, {entry['recipient']}")

    return results

# Streamlit Form
with st.form("reference_form"):
    name = st.text_input("Name")
    recipient = st.text_input("Recipient")
    subject = st.text_input("Subject")
    remarks = st.text_area("Remarks")

    submitted = st.form_submit_button("Generate Reference Number")

    if submitted:
        data = [{
            'name': name,
            'recipient': recipient,
            'subject': subject,
            'remarks': remarks
        }]

        results = generate_reference_numbers(data)
        for result in results:
            st.success(result)

# Run the Streamlit app
if __name__ == "__main__":
    st.write("App is running!")
