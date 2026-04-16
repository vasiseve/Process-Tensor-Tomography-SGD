<script setup>
import { computed, ref } from 'vue'

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const paperPdf = asset('process-tensor-tomography-sgd.pdf')
const supplementPdf = asset('process-tensor-tomography-sgd-supplement.pdf')
const personalSite = 'https://vasiseve.github.io/'
const codeRepo = 'https://github.com/vasiseve/Process-Tensor-Tomography-of-SGD-Measuring-Non-Markovian-Memory-via-Back-Flow-of-Distinguishability'

const activeResult = ref('prevalence')

const callouts = [
  {
    label: 'If you only read one thing',
    text:
      'The paper asks whether a fixed second training intervention depends only on the model’s current observed predictions, or also on the recent path that produced them. If a common second intervention makes two histories more distinguishable, training is not behaving as a memoryless observable process at that scale.',
  },
  {
    label: 'Core concept',
    text:
      'Back-flow of distinguishability means that a common second intervention amplifies the difference between two training histories instead of contracting it.',
  },
  {
    label: 'Main contribution',
    text:
      'A measurement-first witness of short-horizon training memory, plus a causal-break intervention that tests whether optimizer state carries the effect.',
  },
]

const protocolSteps = [
  ['Choose two first interventions', 'Apply A and A prime to create two nearby but distinct training histories.'],
  ['Measure after one step', 'Compare predictive distributions on a fixed probe set and compute D1.'],
  ['Apply the same second intervention', 'Run the common intervention B on both branches.'],
  ['Measure again', 'Compare the two resulting predictive distributions and compute D2.'],
  ['Test for back-flow', 'If Delta_BF = D2 - D1 is positive, the common second step amplified distinguishability.'],
]

const theoryItems = [
  ['Operational Markov condition', 'For a fixed second intervention B, the future observable is described by a single channel acting on the first-step observable.'],
  ['No back-flow under a channel', 'Any contractive divergence must satisfy D2 <= D1 if that channel description is valid.'],
  ['Positive back-flow falsifies the condition', 'Delta_BF > 0 witnesses failure of the observable-level Markov condition for the chosen intervention and observable.'],
  ['Causal break mechanism test', 'Resetting optimizer buffers before B cuts a memory channel while keeping parameters fixed.'],
  ['Perturbative trends', 'Momentum, batch overlap, and longer micro-step sequences should strengthen back-flow.'],
]

const results = {
  prevalence: {
    tab: 'Prevalence',
    title: 'Positive back-flow is common without a break.',
    body:
      'Across CIFAR-100 and Imagenette, and across several vision backbones, the no-break condition frequently produces positive back-flow. The common second intervention often amplifies differences between histories rather than washing them out.',
    stat: 'Measured across SmallCNN, ResNet-18, VGG-11, MobileNetV2, and ViT-B/16.',
  },
  break: {
    tab: 'Causal break',
    title: 'Resetting optimizer state changes the effect.',
    body:
      'When optimizer buffers are reset before the second intervention, back-flow often collapses, shrinks, or flips sign. This links the witness to a concrete memory pathway: optimizer carryover.',
    stat: 'The causal break keeps parameters fixed but cuts optimizer-state history.',
  },
  regimes: {
    tab: 'Regimes',
    title: 'High-momentum and high-overlap settings amplify memory.',
    body:
      'The strongest effects appear where the perturbative picture predicts them: momentum preserves branch differences, overlap lets B resonate with A, and repeated micro-steps compound the effect.',
    stat: 'Negative-control regimes stay near zero.',
  },
  mechanisms: {
    tab: 'Mechanisms',
    title: 'Back-flow aligns with order sensitivity and momentum alignment.',
    body:
      'Non-commutativity of short sequences and pre-B momentum-gradient alignment both track the scalar witness, making the mechanism visible beyond a single aggregate statistic.',
    stat: 'AB versus BA and momentum-alignment diagnostics support the same story.',
  },
}

const activeResultCard = computed(() => results[activeResult.value])

const figures = [
  {
    src: 'figures/tv_regime_summary_ci__cifar100.png',
    title: 'CIFAR-100 regime summary',
    caption: 'Back-flow varies strongly across regimes and break conditions.',
  },
  {
    src: 'figures/tv_regime_summary_ci__imagenette.png',
    title: 'Imagenette regime summary',
    caption: 'The same witness remains informative beyond CIFAR-100.',
  },
  {
    src: 'figures/tv_break_scatter.png',
    title: 'Causal break scatter',
    caption: 'Resetting optimizer buffers shifts the observed back-flow distribution.',
  },
  {
    src: 'figures/tv_top10_positive.png',
    title: 'Top positive witnesses',
    caption: 'The largest positive cases identify regimes where recent history is most visible.',
  },
  {
    src: 'figures/evidence_scatter__delta_vs_momalign.png',
    title: 'Momentum alignment',
    caption: 'Pre-B alignment between momentum and the upcoming gradient predicts larger back-flow.',
  },
  {
    src: 'figures/evidence_scatter__delta_vs_noncomm_slope.png',
    title: 'Order sensitivity',
    caption: 'Back-flow tracks non-commutativity of short intervention sequences.',
  },
  {
    src: 'figures/dose_response__delta_vs_Amu.png',
    title: 'Dose response',
    caption: 'Regime parameters modulate the strength of observable memory.',
  },
  {
    src: 'figures/traj__cifar100__vit_b16__resonant_strong__early__br0.png',
    title: 'Function-space trajectory',
    caption: 'Branch trajectories separate when the optimizer carries history into B.',
  },
]

const faqs = [
  ['What does positive back-flow mean?', 'The common second intervention made two histories more distinguishable than they were after the first step alone.'],
  ['Does this mean SGD is fundamentally non-Markovian?', 'Not necessarily. The claim is about the chosen observable, not the fully augmented latent training state.'],
  ['Why is the causal break important?', 'It resets optimizer buffers while keeping parameters fixed, directly testing whether optimizer state carries the observed memory.'],
  ['Why use predictive distributions?', 'They provide a model-agnostic observable that can be compared operationally across histories.'],
  ['Why TV, JS, and Hellinger?', 'They are bounded, interpretable divergences that contract under stochastic maps, matching the witness argument.'],
  ['Is back-flow always bad?', 'No. The witness measures memory; whether that memory is helpful or harmful depends on the training design.'],
]
</script>

<template>
  <main>
    <nav class="topbar" aria-label="Page sections">
      <a href="#overview">Overview</a>
      <a href="#protocol">Protocol</a>
      <a href="#theory">Theory</a>
      <a href="#results">Results</a>
      <a href="#figures">Figures</a>
      <a href="#faq">FAQ</a>
      <a href="#paper">Paper</a>
    </nav>

    <article class="paper-page">
      <header class="paper-header" id="overview">
        <h1>Process-Tensor Tomography of SGD</h1>
        <p class="subtitle">Measuring non-Markovian memory in training via back-flow of distinguishability</p>
        <p class="authors">Vasileios Sevetlidis and George Pavlidis</p>
        <div class="actions" aria-label="Primary links">
          <a class="button primary" :href="paperPdf" target="_blank" rel="noreferrer">Read the PDF</a>
          <a class="button" :href="supplementPdf" target="_blank" rel="noreferrer">Supplement</a>
          <a class="button" :href="personalSite" target="_blank" rel="noreferrer">Author site</a>
        </div>
      </header>

      <section class="opening">
        <div class="lead-copy">
          <p>
            Training is often analyzed as if it were approximately memoryless.
            Once we know the current parameters, and perhaps optimizer buffers,
            the next update is usually treated as depending only on the present
            state.
          </p>
          <p>
            Practical training is not always so clean. Momentum, repeated
            examples, correlated mini-batches, augmentation sequences, schedules,
            and optimizer state can carry short-horizon memory from one step into
            the next.
          </p>
        </div>
        <aside class="question-box">
          <span>Core question</span>
          <strong>When does training really behave as memoryless, and when does recent history still matter?</strong>
        </aside>
      </section>

      <section class="callout-grid" aria-label="Key ideas">
        <article v-for="item in callouts" :key="item.label" class="callout">
          <p>{{ item.label }}</p>
          <strong>{{ item.text }}</strong>
        </article>
      </section>

      <section id="protocol" class="split-section">
        <div>
          <h2>The simplest witness uses two steps</h2>
          <p>
            Choose two alternative first interventions, A and A prime. Then apply
            the same second intervention B to both branches. If B acts as a
            single history-independent channel on the observed model state,
            distinguishability should not increase.
          </p>
          <p class="distinction">
            If a common second intervention makes two histories more
            distinguishable, it is not behaving as a memoryless observable
            channel.
          </p>
        </div>
        <figure class="diagram-panel">
          <div class="branch-diagram" aria-label="Two-step intervention protocol">
            <div>A</div>
            <div>A'</div>
            <span>B</span>
            <span>B</span>
            <strong>D1</strong>
            <strong>D2</strong>
          </div>
          <figcaption>
            Compare predictive distributions after the first interventions and
            after a shared second intervention. Back-flow is D2 - D1.
          </figcaption>
        </figure>
      </section>

      <section class="equation-band">
        <p class="equation">Delta<sub>BF</sub> = D<sub>2</sub> - D<sub>1</sub></p>
        <p>
          If Delta<sub>BF</sub> is positive, distinguishability flowed back into
          the observed dynamics. The second intervention cannot be represented as
          a single channel acting only on the measured one-step output.
        </p>
      </section>

      <section>
        <h2>Estimator and protocol</h2>
        <div class="step-list">
          <article v-for="([title, text], index) in protocolSteps" :key="title" class="step-card">
            <span>{{ index + 1 }}</span>
            <div>
              <h3>{{ title }}</h3>
              <p>{{ text }}</p>
            </div>
          </article>
        </div>
      </section>

      <section id="theory" class="split-section">
        <div>
          <h2>Why the witness has force</h2>
          <p>
            The logic is a data-processing argument. If a single stochastic map
            describes the second-step observable, contractive divergences such as
            TV, JS, and Hellinger cannot increase under that map.
          </p>
          <p>
            Positive back-flow is therefore a falsification witness for the
            operational Markov condition at the chosen observable and intervention.
          </p>
        </div>
        <div class="guarantee-list">
          <article v-for="([title, text]) in theoryItems" :key="title">
            <h3>{{ title }}</h3>
            <p>{{ text }}</p>
          </article>
        </div>
      </section>

      <section class="analogy-band">
        <h2>The causal break cuts a memory pathway</h2>
        <p>
          Resetting optimizer buffers immediately before B keeps the parameters
          fixed but removes optimizer-state carryover. If back-flow collapses
          after this intervention, optimizer state is implicated as a carrier of
          the observed memory.
        </p>
      </section>

      <section id="results" class="results-section">
        <div>
          <h2>What the empirical story shows</h2>
          <div class="tabs" role="tablist" aria-label="Result views">
            <button
              v-for="(item, key) in results"
              :key="key"
              type="button"
              :class="{ active: activeResult === key }"
              @click="activeResult = key"
            >
              {{ item.tab }}
            </button>
          </div>
        </div>
        <article class="result-reader">
          <h3>{{ activeResultCard.title }}</h3>
          <p>{{ activeResultCard.body }}</p>
          <strong>{{ activeResultCard.stat }}</strong>
        </article>
      </section>

      <section id="figures" class="figure-gallery">
        <h2>Selected figures from the paper</h2>
        <div class="figure-grid">
          <figure v-for="figure in figures" :key="figure.src">
            <img :src="asset(figure.src)" :alt="figure.title" loading="lazy" />
            <figcaption>
              <strong>{{ figure.title }}</strong>
              {{ figure.caption }}
            </figcaption>
          </figure>
        </div>
      </section>

      <section class="split-section">
        <div>
          <h2>Scope and boundaries</h2>
          <p>
            The witness measures observable-level memory, not metaphysical
            non-Markovianity of the fully augmented training state. If one
            includes all hidden variables, buffers, and random seeds, the latent
            process may still be Markovian.
          </p>
          <p>
            The claim is sharper and more useful: at the level of predictive
            distributions on a probe set, the effect of B can depend on how the
            current observation was produced.
          </p>
        </div>
        <figure class="featured-figure">
          <img :src="asset('figures/tv_mean_delta_distribution.png')" alt="Distribution of mean back-flow" loading="lazy" />
          <figcaption>
            A good witness has positive cases and clean null-like regimes; the
            distribution summarizes where the effect appears.
          </figcaption>
        </figure>
      </section>

      <section id="faq" class="faq-section">
        <h2>Common questions</h2>
        <div class="faq-list">
          <article v-for="([question, answer]) in faqs" :key="question">
            <h3>{{ question }}</h3>
            <p>{{ answer }}</p>
          </article>
        </div>
      </section>

      <section class="closing">
        <h2>Training remembers more than the memoryless picture suggests</h2>
        <p>
          By measuring back-flow of distinguishability and testing its collapse
          under optimizer resets, the paper shows that modern training often
          carries an operational memory that is real, measurable, and manipulable.
        </p>
        <p class="link-row">
          <a :href="personalSite" target="_blank" rel="noreferrer">Vasileios Sevetlidis</a>
          <a :href="codeRepo" target="_blank" rel="noreferrer">Reproducibility code</a>
          <a :href="paperPdf" target="_blank" rel="noreferrer">Paper PDF</a>
          <a :href="supplementPdf" target="_blank" rel="noreferrer">Supplement</a>
        </p>
      </section>

      <section id="paper" class="paper-embed">
        <div>
          <h2>Full PDF</h2>
          <p>
            The manuscript and supplement are bundled with this project page so
            the exposition and source paper stay together.
          </p>
          <div class="actions left">
            <a class="button primary" :href="paperPdf" target="_blank" rel="noreferrer">Open paper</a>
            <a class="button" :href="supplementPdf" target="_blank" rel="noreferrer">Open supplement</a>
          </div>
        </div>
        <iframe :src="paperPdf" title="Process-Tensor Tomography of SGD PDF"></iframe>
      </section>
    </article>
  </main>
</template>
