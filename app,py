import streamlit as st
import re
import pandas as pd

# =========================================================
# CONFIG - MOBILE NUMBER NUMEROLOGY
# =========================================================

# Best root totals for mobile numbers
# 5 = communication, movement, networking
# 6 = harmony, attraction, relationships
# 1 = leadership, visibility
# 3 = growth, creativity
# 9 = power, influence (can be intense)
BEST_TOTALS = {
    5: 30,
    6: 26,
    1: 20,
    3: 18,
    9: 12
}

# Weaker / avoid totals
BAD_TOTALS = {
    4: -18,
    8: -22,
    7: -10,
    2: -4
}

# Digit scoring for mobile number
GOOD_DIGITS = {
    '5': 7,   # communication
    '6': 7,   # harmony
    '1': 5,   # identity
    '3': 5,   # creativity
    '9': 3,   # influence
    '0': 2    # amplifier
}

BAD_DIGITS = {
    '4': -6,  # disruption
    '8': -8,  # heavy
    '7': -3,  # detached
    '2': -1   # too passive if overused
}


# =========================================================
# HELPERS
# =========================================================
def digital_root(n):
    while n > 9:
        n = sum(int(d) for d in str(n))
    return n


def extract_mobile_digits(mobile_number):
    digits = re.findall(r'\d', mobile_number)
    if len(digits) < 10:
        return None
    return ''.join(digits[-10:])  # use last 10 digits


def life_path_number(dob):
    digits = [int(d) for d in dob if d.isdigit()]
    if not digits:
        return None
    return digital_root(sum(digits))


def count_repeats(num):
    freq = {}
    for d in num:
        freq[d] = freq.get(d, 0) + 1
    return freq


def sequence_smoothness(num):
    jumps = [abs(int(num[i]) - int(num[i+1])) for i in range(len(num)-1)]
    avg_jump = sum(jumps) / len(jumps)

    if avg_jump <= 1.5:
        return 10, f"Very smooth number flow (avg jump {avg_jump:.2f})"
    elif avg_jump <= 3:
        return 5, f"Moderately smooth flow (avg jump {avg_jump:.2f})"
    elif avg_jump <= 5:
        return -3, f"Jerky number flow (avg jump {avg_jump:.2f})"
    else:
        return -7, f"Very rough number flow (avg jump {avg_jump:.2f})"


def count_good_bad_digits(num):
    good_count = sum(1 for d in num if d in GOOD_DIGITS)
    bad_count = sum(1 for d in num if d in BAD_DIGITS)
    return good_count, bad_count


# =========================================================
# VERSION 1 - SINGLE NUMBER ENGINE
# =========================================================
def detailed_score_mobile_number(mobile_number):
    num = extract_mobile_digits(mobile_number)
    if not num:
        return None

    total = sum(int(d) for d in num)
    root = digital_root(total)

    score = 50
    reasons = []

    # 1) Root total scoring
    if root in BEST_TOTALS:
        score += BEST_TOTALS[root]
        reasons.append(f"Excellent root total {root} for mobile number")
    elif root in BAD_TOTALS:
        score += BAD_TOTALS[root]
        reasons.append(f"Weak / avoid root total {root}")
    else:
        reasons.append(f"Neutral root total {root}")

    # 2) Digit scoring
    for d in num:
        score += GOOD_DIGITS.get(d, 0)
        score += BAD_DIGITS.get(d, 0)

    good_count, bad_count = count_good_bad_digits(num)

    if good_count > bad_count:
        reasons.append(f"Contains more supportive digits ({good_count} good vs {bad_count} harsh)")
    elif bad_count > good_count:
        reasons.append(f"Contains more harsh digits ({bad_count} harsh vs {good_count} supportive)")
    else:
        reasons.append("Digit quality is balanced")

    # 3) Repeat logic
    repeats = count_repeats(num)

    if repeats.get('5', 0) >= 2:
        score += 8
        reasons.append("Repeated 5 detected (excellent communication/networking energy)")
    if repeats.get('6', 0) >= 2:
        score += 8
        reasons.append("Repeated 6 detected (strong harmony/attraction energy)")
    if repeats.get('1', 0) >= 2:
        score += 5
        reasons.append("Repeated 1 detected (visibility/confidence boost)")
    if repeats.get('3', 0) >= 2:
        score += 5
        reasons.append("Repeated 3 detected (growth/creativity support)")

    if repeats.get('4', 0) >= 2:
        score -= 10
        reasons.append("Repeated 4 detected (communication disruption risk)")
    if repeats.get('8', 0) >= 2:
        score -= 12
        reasons.append("Repeated 8 detected (heavy / stressful vibration)")
    if repeats.get('7', 0) >= 3:
        score -= 8
        reasons.append("Too many 7s detected (detached / isolated energy)")

    # 4) Sequence smoothness
    seq_score, seq_reason = sequence_smoothness(num)
    score += seq_score
    reasons.append(seq_reason)

    # 5) Pattern bonuses
    if num[-4:] == num[-4:][::-1]:
        score += 6
        reasons.append("Balanced ending pattern detected")

    if num.endswith("00"):
        score += 4
        reasons.append("Ends with 00 (amplified stable ending)")

    # 6) Collision combos
    if '4' in num and '8' in num:
        score -= 10
        reasons.append("4 + 8 combination present (rough / blocked energy)")
    if '7' in num and '8' in num:
        score -= 6
        reasons.append("7 + 8 combination present (detached + heavy)")
    if '5' in num and '6' in num:
        score += 6
        reasons.append("5 + 6 combination present (excellent communication + harmony)")
    if '1' in num and '5' in num:
        score += 5
        reasons.append("1 + 5 combination present (good leadership + networking)")
    if '3' in num and '6' in num:
        score += 5
        reasons.append("3 + 6 combination present (growth + attraction)")

    # 7) Premium / attractive pattern bonus
    if re.search(r'(\d)\1\1', num):  # 3 repeated digits in a row
        score += 4
        reasons.append("Memorable repeating pattern detected")

    if re.search(r'123|234|345|456|567|678|789', num):
        score += 6
        reasons.append("Ascending sequence detected (good flow and memorability)")

    if re.search(r'321|432|543|654|765|876|987', num):
        score += 3
        reasons.append("Descending sequence detected (acceptable flow)")

    score = max(0, min(100, score))

    if score >= 85:
        decision = "BUY IT ✅"
    elif score >= 70:
        decision = "GOOD / SAFE TO BUY 👍"
    elif score >= 55:
        decision = "CONSIDER IF YOU REALLY LIKE IT 🤔"
    else:
        decision = "AVOID ❌"

    return {
        "Mobile Number": mobile_number,
        "Digits": num,
        "Digit Sum": total,
        "Root": root,
        "Score (%)": score,
        "Decision": decision,
        "Reasons": reasons
    }


# =========================================================
# VERSION 2 - MULTI COMPARE
# =========================================================
def simple_compare_score(mobile_number):
    result = detailed_score_mobile_number(mobile_number)
    if not result:
        return None
    return {
        "Mobile Number": result["Mobile Number"],
        "Digits": result["Digits"],
        "Root": result["Root"],
        "Score (%)": result["Score (%)"],
        "Decision": result["Decision"]
    }


# =========================================================
# VERSION 3 - ADVANCED MATCHING
# =========================================================
def advanced_score_mobile_number(mobile_number, user_lp=None, wife_lp=None):
    base = detailed_score_mobile_number(mobile_number)
    if not base:
        return None

    score = base["Score (%)"]
    root = base["Root"]
    reasons = list(base["Reasons"])

    user_match = "No"
    wife_match = "No"

    # Personal match
    if user_lp:
        if root == user_lp:
            score += 15
            user_match = "Strong"
            reasons.append(f"Strong personal DOB match (Root {root} = Your Life Path {user_lp})")
        elif abs(root - user_lp) == 1:
            score += 8
            user_match = "Good"
            reasons.append(f"Good personal DOB compatibility (Root {root} near Your Life Path {user_lp})")
        else:
            user_match = "Weak"

    # Wife match
    if wife_lp:
        if root == wife_lp:
            score += 10
            wife_match = "Strong"
            reasons.append(f"Strong wife compatibility (Root {root} = Wife Life Path {wife_lp})")
        elif abs(root - wife_lp) == 1:
            score += 5
            wife_match = "Good"
            reasons.append(f"Good wife compatibility (Root {root} near Wife Life Path {wife_lp})")
        else:
            wife_match = "Weak"

    score = max(0, min(100, score))

    if score >= 90:
        final_tag = "EXCELLENT PICK ⭐"
    elif score >= 80:
        final_tag = "VERY GOOD PICK ✅"
    elif score >= 65:
        final_tag = "DECENT OPTION 👍"
    else:
        final_tag = "NOT RECOMMENDED ❌"

    return {
        "Mobile Number": base["Mobile Number"],
        "Digits": base["Digits"],
        "Root": root,
        "Base Score (%)": base["Score (%)"],
        "Final Score (%)": score,
        "Your Match": user_match,
        "Wife Match": wife_match,
        "Recommendation": final_tag,
        "Reasons": reasons
    }


# =========================================================
# UI
# =========================================================
st.set_page_config(page_title="Mobile Number Numerology Analyzer", layout="wide")

st.title("📱 Mobile Number Numerology Analyzer")
st.markdown("### Analyze, compare, and choose the best mobile number")

tab1, tab2, tab3 = st.tabs([
    "🔍 Single Number Checker",
    "📊 Compare Multiple Numbers",
    "💑 Advanced DOB Matching"
])

# =========================================================
# TAB 1 - SINGLE CHECKER
# =========================================================
with tab1:
    st.subheader("Single Mobile Number Analysis")
    st.write("Check whether a single mobile number is worth buying based on numerology.")

    single_number = st.text_input(
        "Enter mobile number",
        placeholder="e.g. 9876543210 or +91 9876543210",
        key="single"
    )

    if st.button("Analyze Single Number", key="single_btn"):
        result = detailed_score_mobile_number(single_number)

        if not result:
            st.error("Please enter a valid mobile number with at least 10 digits.")
        else:
            col1, col2, col3 = st.columns(3)
            col1.metric("Digits", result["Digits"])
            col2.metric("Root", result["Root"])
            col3.metric("Score", f"{result['Score (%)']}%")

            st.markdown(f"## {result['Decision']}")

            st.markdown("### Details")
            st.write(f"**Digit Sum:** {result['Digit Sum']}")

            st.markdown("### Reasons")
            for r in result["Reasons"]:
                st.write(f"- {r}")

# =========================================================
# TAB 2 - MULTI COMPARE
# =========================================================
with tab2:
    st.subheader("Compare Up to 10 Mobile Numbers")
    st.write("Compare multiple numbers and sort them from best to worst.")

    compare_input = st.text_area(
        "Enter mobile numbers (comma separated)",
        placeholder="e.g. 9876543210, 9988776655, 9123456789",
        key="compare"
    )

    if st.button("Compare Numbers", key="compare_btn"):
        numbers = [n.strip() for n in compare_input.split(",") if n.strip()]
        numbers = numbers[:10]

        if not numbers:
            st.error("Please enter at least one valid mobile number.")
        else:
            results = []
            for n in numbers:
                r = simple_compare_score(n)
                if r:
                    results.append(r)

            if not results:
                st.error("No valid mobile numbers found.")
            else:
                df = pd.DataFrame(results)
                df = df.sort_values(by="Score (%)", ascending=False).reset_index(drop=True)
                df.index += 1

                st.markdown("### Ranked Comparison Table")
                st.dataframe(df, use_container_width=True)

                best = df.iloc[0]
                st.success(
                    f"🏆 Best Option: **{best['Mobile Number']}** "
                    f"(Score: {best['Score (%)']}%, Root: {best['Root']})"
                )

# =========================================================
# TAB 3 - ADVANCED MATCHING
# =========================================================
with tab3:
    st.subheader("Advanced Mobile Number Matching")
    st.write("Compare mobile numbers with your DOB and wife compatibility.")

    advanced_numbers = st.text_area(
        "Enter mobile numbers (comma separated)",
        placeholder="e.g. 9876543210, 9988776655, 9123456789",
        key="advanced"
    )

    col1, col2 = st.columns(2)
    with col1:
        user_dob = st.text_input("Your DOB", placeholder="DDMMYYYY", key="user_dob")
    with col2:
        wife_dob = st.text_input("Wife DOB (optional)", placeholder="DDMMYYYY", key="wife_dob")

    if st.button("Run Advanced Analysis", key="advanced_btn"):
        numbers = [n.strip() for n in advanced_numbers.split(",") if n.strip()]
        numbers = numbers[:10]

        if not numbers:
            st.error("Please enter at least one valid mobile number.")
        else:
            user_lp = life_path_number(user_dob)
            wife_lp = life_path_number(wife_dob) if wife_dob else None

            info_col1, info_col2 = st.columns(2)
            if user_lp:
                info_col1.info(f"👤 Your Life Path Number: **{user_lp}**")
            else:
                info_col1.warning("👤 Your DOB missing / invalid")

            if wife_lp:
                info_col2.info(f"💑 Wife Life Path Number: **{wife_lp}**")
            else:
                info_col2.warning("💑 Wife DOB not provided / invalid")

            results = []
            for n in numbers:
                r = advanced_score_mobile_number(n, user_lp, wife_lp)
                if r:
                    results.append(r)

            if not results:
                st.error("No valid mobile numbers found.")
            else:
                df = pd.DataFrame(results)
                df = df.sort_values(by="Final Score (%)", ascending=False).reset_index(drop=True)
                df.index += 1

                st.markdown("### Advanced Comparison Table")
                st.dataframe(df[[
                    "Mobile Number",
                    "Digits",
                    "Root",
                    "Base Score (%)",
                    "Final Score (%)",
                    "Your Match",
                    "Wife Match",
                    "Recommendation"
                ]], use_container_width=True)

                best = df.iloc[0]

                st.markdown("## ⭐ Final Recommendation")
                st.success(
                    f"Choose **{best['Mobile Number']}** "
                    f"(Final Score: {best['Final Score (%)']}%, Root: {best['Root']})"
                )

                with st.expander("See Why This Number Was Chosen"):
                    for reason in best["Reasons"]:
                        st.write(f"- {reason}")
