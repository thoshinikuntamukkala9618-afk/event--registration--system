# event--registration--system
to manage event effortlessly

import json
import os

FILE_NAME = "registrations.json"

events = {
    1: {"name": "Python Workshop", "capacity": 3},
    2: {"name": "Web Development Seminar", "capacity": 5},
    3: {"name": "AI and Machine Learning", "capacity": 4}
}


def load_registrations():
    if os.path.exists(FILE_NAME):
        with open(FILE_NAME, "r") as file:
            return json.load(file)
    return {}


def save_registrations(registrations):
    with open(FILE_NAME, "w") as file:
        json.dump(registrations, file, indent=4)


def view_events(registrations):
    print("\n--- Available Events ---")

    for event_id, event in events.items():
        registered = len(registrations.get(str(event_id), []))
        available = event["capacity"] - registered

        print(
            f"{event_id}. {event['name']} | "
            f"Capacity: {event['capacity']} | "
            f"Available seats: {available}"
        )


def register_participant(registrations):
    view_events(registrations)

    try:
        event_id = int(input("\nEnter event ID: "))

        if event_id not in events:
            print("Invalid event ID.")
            return

        name = input("Enter your name: ").strip()
        email = input("Enter your email: ").strip()

        if not name or not email or "@" not in email:
            print("Please enter a valid name and email.")
            return

        key = str(event_id)
        participants = registrations.get(key, [])

        if any(p["email"].lower() == email.lower()
               for p in participants):
            print("You are already registered for this event.")
            return

        if len(participants) >= events[event_id]["capacity"]:
            print("Sorry, this event is full.")
            return

        participants.append({
            "name": name,
            "email": email
        })

        registrations[key] = participants
        save_registrations(registrations)

        print("Registration successful!")
        print("Event:", events[event_id]["name"])

    except ValueError:
        print("Please enter a valid event ID.")


def view_participants(registrations):
    try:
        event_id = int(input("Enter event ID: "))

        if event_id not in events:
            print("Invalid event ID.")
            return

        participants = registrations.get(str(event_id), [])

        print(f"\nParticipants for {events[event_id]['name']}:")

        if not participants:
            print("No registrations yet.")
            return

        for number, participant in enumerate(participants, 1):
            print(
                f"{number}. {participant['name']} - "
                f"{participant['email']}"
            )

    except ValueError:
        print("Please enter a valid event ID.")


def cancel_registration(registrations):
    try:
        event_id = int(input("Enter event ID: "))

        if event_id not in events:
            print("Invalid event ID.")
            return

        email = input("Enter registered email: ").strip()
        key = str(event_id)
        participants = registrations.get(key, [])

        updated = [
            p for p in participants
            if p["email"].lower() != email.lower()
        ]

        if len(updated) == len(participants):
            print("Registration not found.")
            return

        registrations[key] = updated
        save_registrations(registrations)

        print("Registration cancelled successfully.")

    except ValueError:
        print("Please enter a valid event ID.")


def main():
    registrations = load_registrations()

    while True:
        print("\n===== EVENT REGISTRATION SYSTEM =====")
        print("1. View Events")
        print("2. Register for an Event")
        print("3. View Participants")
        print("4. Cancel Registration")
        print("5. Exit")

        choice = input("Enter your choice: ").strip()

        if choice == "1":
            view_events(registrations)
        elif choice == "2":
            register_participant(registrations)
        elif choice == "3":
            view_participants(registrations)
        elif choice == "4":
            cancel_registration(registrations)
        elif choice == "5":
            print("Thank you for using the system!")
            break
        else:
            print("Invalid choice. Please try again.")


if __name__ == "__main__":
    main()
