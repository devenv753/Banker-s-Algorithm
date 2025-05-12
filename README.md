# Banker's Algorithm

Banker's Algorithm using Vector কোডের বিশ্লেষণ:

## 1. ফাংশন সিগনেচার:
bool isSystemSafe(const vector<vector<int>>& maxDemand, 
                  const vector<vector<int>>& currentAllocation,
                  const vector<int>& availableResources,
                  int totalProcesses, 
                  int totalResourceTypes);
প্যারামিটার:

maxDemand: 2D ভেক্টর, যেখানে প্রতিটি প্রসেসের সর্বোচ্চ রিসোর্স চাহিদা সংরক্ষিত থাকে।

currentAllocation: 2D ভেক্টর, যেখানে প্রতিটি প্রসেসের জন্য বর্তমানে বরাদ্দ রিসোর্স সংরক্ষিত থাকে।

availableResources: 1D ভেক্টর, যেখানে সিস্টেমে বর্তমানে উপলব্ধ রিসোর্সের পরিমাণ রাখা হয়।

totalProcesses: সিস্টেমে মোট প্রসেসের সংখ্যা।

totalResourceTypes: সিস্টেমে মোট রিসোর্সের ধরন (যেমন CPU, মেমরি, ইত্যাদি)।

রিটার্ন টাইপ: bool (যদি সিস্টেম সেফ থাকে তাহলে true, অন্যথায় false)

## 2. ভেরিয়েবল ইনিশিয়ালাইজেশন:

vector<vector<int>> remainingNeed(totalProcesses, vector<int>(totalResourceTypes));
vector<bool> isProcessFinished(totalProcesses, false);
vector<int> work = availableResources;
vector<int> safeSequence;
remainingNeed: একটি 2D ভেক্টর, যা প্রতিটি প্রসেসের জন্য রিসোর্সের বাকি চাহিদা হিসাব রাখে। এটি maxDemand - currentAllocation হিসেবে হিসাব করা হয়।

isProcessFinished: একটি বুলিয়ান ভেক্টর, যা প্রতিটি প্রসেসের জন্য চিহ্নিত করে যে প্রসেসটি শেষ হয়েছে কিনা।

work: 1D ভেক্টর, যা সিস্টেমে বর্তমানে উপলব্ধ রিসোর্সের একটি কপি ধারণ করে। এটি পরে রিসোর্স বরাদ্দের জন্য ব্যবহৃত হয়।

safeSequence: একটি ভেক্টর, যা সেফ সিকোয়েন্স (প্রসেসের কার্যকরী ক্রম) সংরক্ষণ করে।

## 3. Need ম্যাট্রিক্স হিসাব করা:

for (int i = 0; i < totalProcesses; ++i)
    for (int j = 0; j < totalResourceTypes; ++j)
        remainingNeed[i][j] = maxDemand[i][j] - currentAllocation[i][j];
এখানে প্রতিটি প্রসেসের জন্য remainingNeed ম্যাট্রিক্স হিসাব করা হয়। এটি একটি গুরুত্বপূর্ণ ধাপ, কারণ এর মাধ্যমে জানা যায় প্রতিটি প্রসেসকে সম্পূর্ণ করতে কোন রিসোর্সের কতটুকু দরকার।

## 4. সেফ সিকোয়েন্স বের করার চেষ্টা:

while (completedProcesses < totalProcesses) {
    bool foundSafeProcess = false;

    for (int i = 0; i < totalProcesses; ++i) {
        if (!isProcessFinished[i]) {
            bool canAllocate = true;

            for (int j = 0; j < totalResourceTypes; ++j) {
                if (remainingNeed[i][j] > work[j]) {
                    canAllocate = false;
                    break;
                }
            }

            if (canAllocate) {
                for (int j = 0; j < totalResourceTypes; ++j)
                    work[j] += currentAllocation[i][j];

                safeSequence.push_back(i);
                isProcessFinished[i] = true;
                foundSafeProcess = true;
                ++completedProcesses;
            }
        }
    }

    if (!foundSafeProcess) {
        cout << "❌ সিস্টেম এখন Safe অবস্থায় নেই! Deadlock হতে পারে।" << endl;
        return false;
    }
}

প্রসেস চেকিং: প্রতিটি প্রসেসের জন্য চেক করা হয়, সে যদি সেফলি সম্পন্ন হতে পারে, অর্থাৎ প্রয়োজনীয় রিসোর্স সিস্টেমে উপলব্ধ থাকলে তা বরাদ্দ করা হয়।

যদি একটি প্রসেস সেফলি সম্পন্ন করা যায়, তখন তার বরাদ্দ করা রিসোর্স সিস্টেমের কাজে ফিরে আসে এবং সেফ সিকোয়েন্সে ওই প্রসেস যুক্ত করা হয়।

Deadlock Check: যদি কোনো প্রসেস সেফলি সম্পন্ন না হয়, তখন সিস্টেম Unsafe বলে ধরে নেওয়া হয় এবং Deadlock হতে পারে।

## 5. Safe Sequence প্রদর্শন:

cout << "✅ সিস্টেম Safe অবস্থায় আছে।" << endl;
cout << "👉 Safe Execution Sequence: ";
for (int p : safeSequence)
    cout << "P" << p << " ";
cout << endl;
সিস্টেম যদি সেফ অবস্থায় থাকে, তাহলে একটি সেফ সিকোয়েন্স প্রদর্শন করা হয়, যা প্রতিটি প্রসেসের কার্যকরী ক্রম।

## 6. মেইন ফাংশন:

int main() {
    int totalProcesses = 5;
    int totalResourceTypes = 3;

    vector<vector<int>> currentAllocation = {
        {0, 1, 0}, 
        {2, 0, 0}, 
        {3, 0, 2}, 
        {2, 1, 1}, 
        {0, 0, 2}
    };

    vector<vector<int>> maxDemand = {
        {7, 5, 3}, 
        {3, 2, 2}, 
        {9, 0, 2}, 
        {2, 2, 2}, 
        {4, 3, 3}
    };

    vector<int> availableResources = {3, 3, 2};

    isSystemSafe(maxDemand, currentAllocation, availableResources, totalProcesses, totalResourceTypes);

    return 0;
}

Data Initialization: এখানে ৫টি প্রসেস এবং ৩টি রিসোর্সের ধরন নিয়ে উদাহরণ দেওয়া হয়েছে। প্রতিটি প্রসেসের জন্য বরাদ্দ এবং সর্বোচ্চ চাহিদা ম্যাট্রিক্স এবং সিস্টেমের বর্তমান ফ্রি রিসোর্স কনফিগার করা হয়েছে।

Safe Check Call: isSystemSafe() ফাংশন কল করে সিস্টেমের সেফ অবস্থান চেক করা হচ্ছে।


নির্বাচিত কনসেপ্টস:
Need Matrix: এই ম্যাট্রিক্সটি প্রতিটি প্রসেসের বাকি রিসোর্স চাহিদা দেখায় যা সম্পূর্ণ করতে প্রয়োজন।

Work Array: এটি সিস্টেমে বর্তমানে উপলব্ধ রিসোর্সকে ধারণ করে, যা পরে বরাদ্দের জন্য ব্যবহৃত হয়।

Safe Sequence: এটি একটি সিকোয়েন্স যা নির্দেশ করে কোন প্রসেসগুলো একে একে সেফভাবে সম্পন্ন হবে।


<br>

## Online Editor: <br>
https://www.programiz.com/online-compiler/4dsmEDDuahLqh <br><br>
https://www.programiz.com/online-compiler/2P7NWoWXqlJZD
