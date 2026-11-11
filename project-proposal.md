Degree level: Master’s

# Project proposal

## 1. The problem and goal

(Explain the problem you want to solve and why it matters. Describe what you
want to improve, how you will measure improvement, and which limits or
requirements must be respected. A short example can help someone outside your
field understand the problem.)

**The problem and goal in my project:**

Analyzing Raman spectroscopy data requires precise curve fitting to
identify molecular vibrational modes, such as those in Carbon
Tetrachloride (CCl_4). Usually, this is a tedious manual process:
a researcher must visually inspect the data, guess initial parameters
(peak center, width, and amplitude), and select the right mathematical
line shape (Lorentzian, Gaussian, or Pseudo-Voigt). If the initial
guess is poor, standard optimization algorithms fail to converge on
the true shape and output completely wrong fits.

The goal of this project is to build an automated, agentic
data-correction loop that intelligently fits Raman peaks without human
intervention. Since the full spectrum is constructed peak by peak,
improvement will be measured by evaluating the goodness-of-fit using
metrics like the residual error (RMSE) and the coefficient of determination
(R^2). Additionally, when using synthetic data, the agent's accuracy will
be scored directly against a known control dataset (ground truth). A strict
requirement is that the agent must respect the physical meaning of the
parameters—for instance, it cannot suggest a negative width or
amplitude for a peak, and the Pseudo-Voigt mixing parameter must
strictly remain between 0 and 1.

**Example:** Imagine you ate a delicious dish at a restaurant and want
to recreate the exact recipe at home based only on taste. If you just
throw random ingredients together (a blind algorithm), you'll fail.
Instead, you need to systematically taste your version, compare it to
your memory of the original dish, and logically adjust the spices
step-by-step until the flavors match perfectly. My agent acts as that
logical chef for molecular data.

## 2. The simulator

(Describe a program or model that could simulate your system and calculate what
happens under different conditions. Explain what inputs it takes and what
results it produces. State whether it can run on your computer and accept inputs
in a text file, such as JSON or YAML. If you have not chosen a simulator yet,
name an option you want to explore and explain what you need to check.)

**The simulator in my project:**

The "simulator" for this project will be a custom Python script that
acts as the mathematical fitting environment, utilizing standard
libraries like `scipy.optimize` to perform curve-fitting. It is
designed to isolate and fit individual peaks one by one, rather than
attempting to fit the entire spectrum simultaneously.

* **Inputs:** For validation, it will generate isolated synthetic Raman
  peaks with added noise. For real application, it will take raw data
  loaded directly from `.txt` or `.csv` files and slice it into
  specific peak regions. In both cases, it receives the agent's chosen
  function type (Gaussian, Lorentzian, or Pseudo-Voigt) and initial
  parameter guesses for that specific peak.
* **Outputs:** The simulator runs the optimization based on the
  agent's inputs. It returns the finalized fitted parameters for the
  single peak, the residual error, and a performance score (comparing
  the agent's fit to the exact ground truth when using synthetic data).

This Python environment will run locally on my PC (e.g., using Spyder)
and easily accepts parameter guesses in simple text files (like JSON or YAML).
By isolating and fitting the spectrum peak-by-peak, we drastically
simplify the parameter space, allowing the agent to reason effectively
about one shape at a time before moving on to the next.

## 3. The agent's role

(Explain what the language-model-based agent will decide or change, and how it
will use the simulator's results to choose its next action. If known, list the
variables it may change and their allowed ranges. Explain what you expect the
language model to contribute: could a simple program that tries different
options and picks the best result achieve the same goal? Include one question
you still need to answer.)

**The agent in my project:**

While the simulator performs the raw mathematical calculation, the
language-model-based agent acts as the automated "spectroscopist."
Its primary goal is to achieve the maximum possible fit between the
experimental data and the mathematical model.

The agent will receive a numerical summary of the simulator's results
(e.g., the residual error) and decide how to adjust the inputs for the
next iteration. It can change two main things:
1. The structural model: Deciding whether to use a Gaussian,
   Lorentzian, or Voigt/Pseudo-Voigt function based on which one best
   captures the physical shape of the specific peak.
2. The initial numerical guesses: Center (x_0), Amplitude (A > 0),
   Width (sigma or gamma > 0), and the mixing fraction.

Could a simple script achieve this? A basic script could blindly try
all three functions and pick the lowest error. However, if the initial
guesses are far off, standard algorithms crash or produce physically
meaningless results. The LLM agent contributes reasoning: if a fit
fails, it can analyze the shape of the error and logically correct 
the initial parameters to guide the algorithm toward the true physical shape.

**One question I still need to answer:** Can the agent successfully
manage to fit the entire Raman spectrum simultaneously with sufficient
accuracy, or is it strictly necessary to isolate and fit the data
peak by peak?

## Explain your project to someone outside your field

(Now describe your whole project again, as if you were explaining it to someone
from another field who has no background in your topic. For example, if you
study chemical engineering, imagine explaining it to a physicist; if you study
aeronautical engineering, imagine explaining it to someone in medicine; if you
study data science, imagine explaining it to a materials engineer. In one or two
short paragraphs, connect all three parts: the problem you want to solve, how
the simulator helps you test ideas, and what the agent will decide or change.
Use everyday language, explain any technical terms you need, and avoid
unexplained abbreviations. Do not simply copy your answers above.)

**My project in simple words:**

Every molecule leaves a unique "fingerprint" of light when hit by
a laser, showing up as a raw graph full of peaks and valleys. To
actually understand the molecule, scientists need to match these rough
hills with perfect mathematical curves. Doing this by hand is like
trying to recreate a complex restaurant dish at home by blindly
guessing the recipe — it is frustrating, slow, and often fails.

My project uses Artificial Intelligence to automate this process. I am
building a virtual "kitchen" (a Python simulator) that takes a recipe
guess, mathematically mixes it, and reports exactly how far off it is
from the real data. The AI agent acts as a logical master chef: it
looks at the feedback, figures out if it just needs a "pinch more
salt" (minor number tweaks) or a completely different cooking method
(switching the curve type entirely), and feeds the new recipe back to
the kitchen until the match is perfect.
