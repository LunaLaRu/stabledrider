Stabled Riding AppStabled is a comprehensive web application designed for equestrians to manage their horses, track goals, log care, record rides, and celebrate milestones. This application is built with React and powered by Google Firebase for a robust and scalable backend.Table of ContentsFeaturesTechnology StackGetting StartedPrerequisitesFirebase SetupInstallationRunning the ApplicationProject StructureSubscription TiersContributingLicenseFeaturesStabled offers a range of features to enhance the riding experience:User Authentication: Secure sign-up and login with email/password.Horse Management:Create and manage detailed profiles for multiple horses (name, breed, discipline, age, sex, color, image).Edit and delete horse profiles.Goal Tracking:Set and track riding and training goals for each horse.Monitor progress towards goals.Care & Cost Tracking:Log various care events (vet visits, farrier, deworming, etc.) with dates, practitioners, and costs.Track expenses associated with horse care.Ride Tracking:Manually log rides with details like date, time, duration, distance, weather, gaits, and notes.Simulated GPS Tracking (Premium): Conceptual implementation for real-time ride tracking, including speed and path data.Milestones:A predefined list of 25 common horse-related milestones.Ability to mark milestones as earned.Social Sharing (Premium): Share earned milestones to social media platforms.User Profile:Edit personal profile information including name, bio, location, preferred discipline, riding level, and profile picture.Subscription Management:Free and Premium tiers with different feature sets.Simulated monthly ($5) and annual ($50) payment options.Ability to upgrade and downgrade subscriptions.Responsive Design: Optimized for seamless use across mobile, tablet, and desktop devices.User Feedback: Integrated message box for in-app notifications.Technology StackFrontend:React: A JavaScript library for building user interfaces.Tailwind CSS: A utility-first CSS framework for rapid and responsive styling.Backend & Database:Firebase: Google's platform for developing mobile and web applications.Firebase Authentication: For user registration and login.Firestore: A flexible, scalable NoSQL cloud database for storing application data (users, horses, goals, rides, care records, milestones).Firebase Storage: For storing user profile pictures and horse images.Getting StartedFollow these instructions to get a copy of the project up and running on your local machine for development and testing purposes.PrerequisitesNode.js (LTS version recommended) and npm (or Yarn) installed.A Google Firebase project set up.Firebase SetupCreate a Firebase Project: Go to the Firebase Console and create a new project.Enable Authentication:In your Firebase project, navigate to "Authentication" > "Sign-in method".Enable "Email/Password" provider.(Optional) Enable other providers like Google, Apple, Facebook if you plan to extend authentication.Set up Firestore Database:In your Firebase project, navigate to "Firestore Database".Click "Create database". Choose "Start in production mode" for security, but remember to set up proper security rules.Firestore Security Rules: For basic functionality, you'll need rules that allow authenticated users to read and write to their own data. Here's a basic example for users and barns collections. For production, review and refine these rules carefully.rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users collection: Each user can read/write their own profile
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }

    // Barns collection: Barn owners can manage their specific barn data
    // Data under barns/{barnId} should be accessible by the owner and associated users (clients/staff)
    match /barns/{barnId} {
      allow read, write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.barnId == barnId;

      // Sub-collections under barns (e.g., horses, goals, careRecords, rides)
      match /{collectionId}/{docId} {
        allow read, write: if request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.barnId == barnId;
      }
    }

    // Earned Milestones: Each user can read/write their own earned milestones
    match /users/{userId}/earnedMilestones/{milestoneId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
Set up Firebase Storage:In your Firebase project, navigate to "Storage".Click "Get started".Storage Security Rules: Similar to Firestore, you'll need rules. Here's a basic example. For production, refine these.rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /profile_images/{userId}/{fileName} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /horse_images/{userId}/{fileName} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    // Allow read for all images if they are intended to be public (e.g., for sharing)
    // match /horse_images/{userId}/{fileName} {
    //   allow read;
    //   allow write: if request.auth != null && request.auth.uid == userId;
    // }
  }
}
Get Your Firebase Configuration:In your Firebase project settings (click the gear icon next to "Project overview"), go to "Project settings" > "General".Scroll down to "Your apps" and select the web app you registered (or add a new one).Copy the firebaseConfig object.InstallationClone the repository:git clone <your-github-repo-url>
cd stabled-riding-app # Or whatever your project folder is named
Install dependencies:npm install
# or
yarn install
Update Firebase Configuration:Open src/App.jsx (or src/App.js).Locate the firebaseConfig constant near the top of the file.Replace {} with your actual Firebase configuration object you copied from the Firebase Console.The __app_id and __initial_auth_token variables are typically provided by specific deployment environments (like Google Canvas). For local development or a standard GitHub deployment, you can leave __app_id as 'default-app-id' and __initial_auth_token as null as they are handled by the AuthProvider's anonymous sign-in fallback.// Global variables provided by the Canvas environment (replace with your actual config for local/GitHub)
const appId = 'your-actual-firebase-app-id'; // You can get this from your firebaseConfig
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_AUTH_DOMAIN",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};
const initialAuthToken = null; // Leave as null for standard email/password or anonymous login
Running the ApplicationTo run the application in development mode:npm start
# or
yarn start
This will open the application in your browser at http://localhost:3000 (or another available port).Project StructureThe core application logic resides within src/App.jsx (or src/App.js).src/App.jsx: Contains all React components and Firebase integration logic.AuthProvider: Manages Firebase authentication state and provides it to other components via React Context.MessageBox: A simple component for displaying success/error messages.AuthPage: Handles user login and signup.HorseManagement: Allows users to add, edit, and view horse profiles.GoalTracking: Manages creation and tracking of riding goals.CareTracking: Enables logging of horse care events and costs.RideTracking: Facilitates manual ride entry and simulates GPS tracking.Milestones: Displays and allows users to earn and share riding milestones.Profile: Allows users to edit their personal profile information.Subscription: Manages subscription tiers (Free/Premium) with simulated payment.Dashboard: Provides a quick overview for logged-in users.AppOverview: General information about the app, visible to all.Subscription TiersStabled offers two main subscription tiers:Free Tier:Limited to 1 horse profile.Limited to 3 active goals.Limited care & cost entries (conceptual limit).Manual ride entry only (no GPS tracking).View milestones (no social sharing).Premium Tier ($5/month or $50/year):All Free Tier features.Unlimited horse profiles.Unlimited goals, care, and ride entries.GPS ride tracking.Full milestone functionality with social sharing.Conceptual AI insights and other advanced features.The payment process is simulated in this code. For a production application, a secure payment gateway like Stripe would be integrated, typically involving Firebase Cloud Functions for server-side webhook handling.ContributingContributions are welcome! If you have suggestions for improvements or find issues, please open an issue or submit a pull request on the GitHub repository.LicenseThis project is open-source and available under the MIT License.
