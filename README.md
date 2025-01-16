# Client-s-architecture
An automated application to get information about clients
import os

# Define the list of companies
companies = {
    "abc": "ABC Company",
    "def": "DEF Company",
    "ghi": "GHI Company",
    "jkl": "JKL Company",
    "mno": "MNO Company",
    "pqr": "PQR Company",
    "stu": "STU Company"
}

# Ensure the folder for saving data exists
data_folder = "company_details"
os.makedirs(data_folder, exist_ok=True)

# Questions for application details
questions = [
    "Remote server license managed by: ",
    "TSScan license managed by: ",
    "365 license managed by: ",
    "RDP Guard managed by: ",
    "DUO: ",
    "Domain Name: ",
    "EHR - PM host/Cloud: ",
    "Server: ",
    "Any License Applications: ",
    "Vipre/EDR/Web Filter: ",
    "Encryption Email: ",
    "Spam Blocker: "
]

def get_company():
    print("\nList of companies:")
    for idx, (acronym, full_name) in enumerate(companies.items(), start=1):
        print(f"{idx}. {full_name} ({acronym})")
    
    # Get the user's selection by number or name
    choice = input("\nEnter the number or name of the company: ").strip().lower()
    
    # If the input is a number, convert to index
    if choice.isdigit():
        idx = int(choice) - 1
        if 0 <= idx < len(companies):
            acronym = list(companies.keys())[idx]
            full_name = companies[acronym]
            return acronym, full_name
    
    # If it's a name or acronym, check for match
    for acronym, full_name in companies.items():
        if choice == acronym or choice == full_name.lower():
            return acronym, full_name
    
    return None, None

def load_existing_data(acronym):
    file_path = os.path.join(data_folder, f"{acronym}.txt")
    if os.path.exists(file_path):
        with open(file_path, "r") as file:
            data = {line.split(":")[0].strip(): line.split(":")[1].strip() for line in file.readlines()}
        return data
    return {}

def update_company_details(acronym, full_name):
    file_path = os.path.join(data_folder, f"{acronym}.txt")
    
    # Load existing data if it exists
    existing_data = load_existing_data(acronym)
    
    details = {}
    
    # Prompt for each question, displaying the existing answer if available
    print(f"\nUpdating details for {full_name} ({acronym}):")
    
    for question in questions:
        key = question.strip(": ")
        current_answer = existing_data.get(key, "Not Provided")
        
        print(f"\n{question}{current_answer}")
        answer = input(f"Enter new value (leave blank to skip): ").strip()
        
        if answer:
            details[key] = answer
        else:
            details[key] = current_answer
    
    # Save the updated details to the file
    with open(file_path, "w") as file:
        for key, value in details.items():
            file.write(f"{key}: {value}\n")
    
    print(f"Details for {full_name} saved successfully.")

def main():
    while True:
        acronym, full_name = get_company()
        if acronym and full_name:
            update_company_details(acronym, full_name)
        else:
            print("Company not found. Please try again.")
        another = input("Do you want to update another company? (yes/no): ").strip().lower()
        if another != "yes":
            break

if __name__ == "__main__":
    main()
