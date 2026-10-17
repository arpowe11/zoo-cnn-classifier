# Assignment Plan: Cats / Dogs / Panda Classifier

## Decision: Option 2 (transfer learning)

**Why:** ~1,000 images per class is small. A pretrained ImageNet backbone (VGG19 as suggested, with
MobileNetV2/ResNet50 as an alternative comparison) reaches high accuracy with little training, runs
acceptably on a laptop, and leaves clear room for the required "improve the accuracy" step
(fine-tuning, augmentation, hyperparameter changes). A small from-scratch CNN is still trained as a
**baseline for comparison only**, which also satisfies the "start with a baseline" guideline.

- Input: 150x150x3, output: 3-way softmax, loss: `categorical_crossentropy`
- Notebook: `notebooks/` (single deliverable notebook), data: `data/images/{cats,dogs,panda}`

---

## Iterative Loop (run after EVERY development step)

After each change, run this checklist before moving on. Log results in the **Experiment Log** below.

1. **Run clean** - Restart kernel and Run All. Must finish with no errors or stale-state dependence.
2. **Sanity checks**
   - Shapes: input batch `(B,150,150,3)`, output `(B,3)`, outputs sum to 1.
   - Class counts and split sizes printed and unchanged unless intentionally changed.
   - No leakage: val/test images never in train; augmentation only on train; fixed random seed.
   - Pixel scaling matches the backbone (`vgg19.preprocess_input` for VGG19, else `/255`).
3. **Evaluate** - Record train/val loss and accuracy, plus test accuracy once at the end of a milestone.
   Look at loss/accuracy curves: gap between train and val = overfitting; both low = underfitting.
4. **Diagnose** - Confusion matrix and per-class precision/recall; view ~8 misclassified images.
5. **Decide the next single change** - Change one variable at a time, based on the diagnosis:
   | Symptom | Next move |
   |---|---|
   | Underfit (train acc low) | Unfreeze top backbone block, raise capacity, lower LR, more epochs |
   | Overfit (val << train) | Augmentation, dropout, weight decay, early stopping, fewer trainable layers |
   | Val loss noisy/unstable | Lower LR, larger batch, LR scheduler (`ReduceLROnPlateau`) |
   | One class weak | Inspect those images, class weights, check for bad/mislabeled files |
6. **Log + save** - Add a row to the Experiment Log, save the best weights (`ModelCheckpoint`), keep notebook outputs.
7. **Stop rule** - Stop when val accuracy gains < ~0.5% across two consecutive experiments, or after the
   experiment budget (about 6-8 runs) is used. Then do the final test evaluation exactly once.

---

## Development Steps

Each step ends by running the loop above.

### Step 0 - Environment
- Confirm `.venv` has `tensorflow`/`keras`, `numpy`, `matplotlib`, `scikit-learn`, `Pillow`; set seeds.
- Check GPU/Metal availability (`tf.config.list_physical_devices`).
- **Exit check:** imports work, versions printed.

### Step 1 - Data exploration (required by the assignment)
- Count images per class; check balance.
- Sample image sizes (width/height distribution); detect corrupt/unreadable/non-RGB files.
- Display a grid of sample images per class.
- Choose and justify the training size (150x150x3).
- **Exit check:** counts table, size stats, image grid, written insights in a markdown cell.

### Step 2 - Data pipeline
- Stratified train/val/test split (e.g. 70/15/15), fixed seed.
- `tf.data` or `image_dataset_from_directory`: resize to 150x150, batch, cache, prefetch, one-hot labels.
- Augmentation layers (flip, small rotation/zoom/contrast), train split only.
- **Exit check:** shapes correct, split sizes printed, augmented samples displayed.

### Step 3 - Baseline
- Small from-scratch CNN (2-3 conv blocks + dense + softmax), Adam, `categorical_crossentropy`.
- Fit with val data; plot loss/accuracy curves.
- **Exit check:** baseline val accuracy logged (this is the number to beat).

### Step 4 - Transfer learning model (main model)
- VGG19 `include_top=False`, `weights="imagenet"`, backbone frozen.
- Head: `GlobalAveragePooling2D` -> `Dense(256, relu)` -> `Dropout` -> `Dense(3, softmax)`.
- Train head only; plot curves.
- **Exit check:** val accuracy logged and compared to baseline.

### Step 5 - Improve accuracy (required by the assignment)
Run as separate, logged experiments, one change each:
1. Fine-tune: unfreeze last conv block, LR about 1e-5.
2. Stronger augmentation and/or dropout.
3. Optimizer comparison (Adam vs RMSprop vs SGD+momentum).
4. Batch size / LR scheduler / early stopping.
5. Optional: swap backbone (MobileNetV2/ResNet50) for comparison.
- **Exit check:** at least 3 documented experiments with a comparison table and plot.

### Step 6 - Final evaluation
- Pick best model by **validation** metrics; evaluate on the test set once.
- Confusion matrix, classification report, misclassified examples.
- **Exit check:** test accuracy reported; matches expectation from val (no big drop).

### Step 7 - Write-up and submission
- Markdown cells explaining: exploration insights, architecture choices, hyperparameters, what improved
  accuracy and why, final results, limitations.
- Restart kernel, Run All a last time, confirm all outputs render, then export/submit.
- **Exit check:** notebook runs top to bottom, every required item in `instructions.md` is covered.

---

## Requirements Coverage (from `docs/instructions.md`)

- [ ] Option chosen (Option 2) and stated in the notebook
- [ ] Data exploration: image count, sample images, image size choice
- [ ] Baseline model fit on train/test data
- [ ] Loss/accuracy plots evaluated
- [ ] Architecture and/or hyperparameter variations tried
- [ ] Accuracy improved over baseline, with explanation
- [ ] Softmax output + `categorical_crossentropy` loss

---

## Experiment Log

| # | Date | Change (one variable) | Train acc | Val acc | Val loss | Notes / next move |
|---|------|-----------------------|-----------|---------|----------|-------------------|
| 0 | | Baseline small CNN | | | | |
| 1 | | VGG19 frozen + new head | | | | |
