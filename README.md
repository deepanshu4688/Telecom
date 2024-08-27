# telecom
import phonenumbers
from phonenumbers import geocoder, carrier
import pandas as pd

# Load contact information from CSV file
contacts_csv_data = """type,name,phone_number,address
police_station,Police Station 1,1234567890,123 Main St, Bhopal
hospital,Hospital 1,0987654321,456 Elm St, Bhopal
hotel,Hotel 1,1122334455,789 Maple Ave, Bhopal
bus_station,Bus Station 1,2233445566,101 Pine St, Bhopal
railway_station,Railway Station 1,3344556677,202 Oak St, Bhopal"""

spam_numbers = ['1234567890', '0987654321']

contacts_df = pd.read_csv(pd.compat.StringIO(contacts_csv_data))

# Function to check if a number is spam
def is_spam(number):
    return number in spam_numbers

# Function to get contact details
def get_contact_details(contact_type):
    contacts = contacts_df[contacts_df['type'] == contact_type]
    return contacts

# Function to get number details
def get_number_details(number):
    parsed_number = phonenumbers.parse(number, "IN")
    number_info = {
        "valid": phonenumbers.is_valid_number(parsed_number),
        "location": geocoder.description_for_number(parsed_number, "en"),
        "carrier": carrier.name_for_number(parsed_number, "en")
    }
    return number_info

# Function to display emergency contacts
def display_emergency_contacts():
    print("\nEmergency Contacts:")
    for contact_type in ['police_station', 'hospital', 'hotel', 'bus_station', 'railway_station']:
        contacts = get_contact_details(contact_type)
        print(f"\n{contact_type.replace('_', ' ').title()}s:")
        for index, contact in contacts.iterrows():
            print(f"Name: {contact['name']}, Phone: {contact['phone_number']}, Address: {contact['address']}")

# Main function
def main():
    print("Telecom Help Application\n")

    while True:
        number = input("Enter a phone number to check or type 'exit' to quit: ")
        
        if number.lower() == 'exit':
            break
        
        if is_spam(number):
            print("Warning: This number is marked as spam.")
        else:
            details = get_number_details(number)
            if details['valid']:
                print(f"Number Details:\n Location: {details['location']}\n Carrier: {details['carrier']}")
            else:
                print("This number is not valid.")
        
        display_emergency_contacts()
        print("\n" + "="*50 + "\n")

if __name__ == "__main__":
    main()
